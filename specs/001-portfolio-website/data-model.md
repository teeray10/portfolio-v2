# Data Model: Portfolio Website

**Feature**: 001-portfolio-website
**Phase**: 1 — Design
**Date**: 2026-05-15
**Source**: spec.md entities + research.md (Content Collections + Zod schemas)

---

## Overview

All content is managed as Astro Content Collections — type-safe, file-based data with
Zod schema validation at build time. There is no runtime database. Changes to content
are made by editing files in `src/content/`, which triggers a rebuild.

---

## Entity: DeveloperProfile

**Purpose**: The single identity anchor for the entire site. Rendered in the Hero section,
nav, footer, and SEO metadata. There is exactly one instance.

**Storage**: `src/content/profile/index.json`

**Schema**:

```typescript
const profileSchema = z.object({
  name: z.string(), // "Jane Doe"
  title: z.string(), // "Senior Frontend Engineer"
  tagline: z.string().max(120), // Positioning statement, ≤120 chars
  avatar: z.string(), // Path to image in public/images/
  email: z.string().email(), // Contact fallback address
  location: z.string().optional(), // "London, UK" — optional
  links: z.array(
    z.object({
      label: z.string(), // "GitHub"
      url: z.string().url(), // "https://github.com/..."
      icon: z.string(), // Icon identifier (e.g., "github")
    }),
  ),
});
```

**Constraints**:

- Exactly one profile entry MUST exist; build fails if absent
- `tagline` ≤ 120 characters (enforced by Zod)
- `avatar` MUST reference an image that exists in `public/`
- `links` MUST include at minimum one of: GitHub, LinkedIn

---

## Entity: Project

**Purpose**: Represents a discrete piece of work shown in the Projects section.
Multiple entries; ordered by `order` field (ascending), with `featured` entries
displayed more prominently.

**Storage**: `src/content/projects/<slug>.json` (one file per project)

**Schema**:

```typescript
const projectSchema = z.object({
  title: z.string(),
  slug: z.string().regex(/^[a-z0-9-]+$/), // URL-safe identifier
  description: z.string().max(280), // Short description, ≤280 chars
  longDescription: z.string().optional(), // Extended detail, no max
  tags: z.array(z.string()).min(1).max(8), // Technology/skill tags
  image: z.string(), // Path to project visual in public/
  imageAlt: z.string(), // Required alt text
  liveUrl: z.string().url().optional(), // Live deployment URL
  sourceUrl: z.string().url().optional(), // Repository URL
  featured: z.boolean().default(false), // Prominent display treatment
  order: z.number().int().positive(), // Display order (lower = earlier)
  year: z.number().int().min(2000).max(2099),
});
```

**Constraints**:

- At least one of `liveUrl` or `sourceUrl` MUST be present (validated in component,
  not schema — allows drafts without links during development)
- `description` ≤ 280 characters (Zod enforced)
- `tags` between 1 and 8 items
- `image` MUST reference a file that exists in `public/`
- At least 3 projects MUST exist before public deployment (per spec Assumptions)

**Derived**: The collection is sorted by `featured` (true first), then `order` ascending
at query time.

---

## Entity: Skill

**Purpose**: A named technical or professional competency, grouped into categories and
assigned a proficiency tier. Displayed in the Skills section.

**Storage**: `src/content/skills/<category-slug>.json` (one file per category,
containing all skills in that category)

**Proficiency tiers** (enum — no percentage values ever):

```typescript
const ProficiencyTier = z.enum(['expert', 'proficient', 'familiar']);
```

**Schema (per category file)**:

```typescript
const skillCategorySchema = z.object({
  category: z.string(), // "Frontend Development"
  slug: z.string().regex(/^[a-z0-9-]+$/),
  order: z.number().int().positive(), // Display order of category
  skills: z
    .array(
      z.object({
        name: z.string(), // "TypeScript"
        tier: ProficiencyTier,
        icon: z.string().optional(), // Icon identifier
        order: z.number().int().positive(), // Order within category
      }),
    )
    .min(1),
});
```

**Constraints**:

- `tier` MUST be one of `expert | proficient | familiar` — no custom values
- No numeric scores, percentages, or star ratings anywhere in the schema
- Each category file MUST have at least 1 skill

---

## Entity: ExperienceEntry

**Purpose**: A career role, displayed in the Experience section in reverse chronological
order (most recent first).

**Storage**: `src/content/experience/<slug>.json` (one file per role)

**Schema**:

```typescript
const experienceSchema = z.object({
  role: z.string(), // "Senior Frontend Engineer"
  company: z.string(),
  companyUrl: z.string().url().optional(),
  startDate: z.string().regex(/^\d{4}-\d{2}$/), // "YYYY-MM" format
  endDate: z.union([
    z.string().regex(/^\d{4}-\d{2}$/), // "YYYY-MM" format
    z.literal('present'),
  ]),
  location: z.string().optional(), // "London, UK" or "Remote"
  accomplishments: z.array(z.string()).min(2).max(5),
  order: z.number().int().positive(), // Explicit order (lower = more recent)
});
```

**Constraints**:

- `startDate` MUST be before `endDate` (validated in component)
- `accomplishments` between 2 and 5 items per entry
- `endDate: "present"` reserved for current role only

**Derived**: Displayed duration calculated at render time from `startDate` / `endDate`
as "X years Y months" alongside the formatted date range.

---

## Entity: ContactMessage

**Purpose**: Ephemeral — user-submitted contact form data sent to the third-party
form service. Not stored in any first-party system.

**Lifecycle**: Created client-side → POSTed to form service endpoint → discarded;
no persistence on this site.

**Shape** (not a Content Collection — client-side form state only):

```typescript
interface ContactMessage {
  name: string; // Required, non-empty
  email: string; // Required, valid email format
  message: string; // Required, ≥10 chars, ≤2000 chars
  _honeypot?: string; // Hidden field; submission rejected if non-empty
}
```

**Validation**: All fields validated client-side before submission. `_honeypot` is a
hidden input that bots fill in; if non-empty the form silently succeeds without sending.

---

## Content Collection Configuration

**File**: `src/content/config.ts`

```typescript
import {defineCollection} from 'astro:content';
import {z} from 'astro:schema';

export const collections = {
  profile: defineCollection({type: 'data', schema: profileSchema}),
  projects: defineCollection({type: 'data', schema: projectSchema}),
  skills: defineCollection({type: 'data', schema: skillCategorySchema}),
  experience: defineCollection({type: 'data', schema: experienceSchema}),
};
```

All schemas use `type: "data"` (JSON files, not Markdown). Content is queried with
`getCollection()` and `getEntry()` from `astro:content` in component frontmatter.

---

## State & Lifecycle Summary

| Entity           | Storage                   | Mutability      | Owner             |
| ---------------- | ------------------------- | --------------- | ----------------- |
| DeveloperProfile | `src/content/profile/`    | Build-time only | Developer (code)  |
| Project          | `src/content/projects/`   | Build-time only | Developer (code)  |
| Skill            | `src/content/skills/`     | Build-time only | Developer (code)  |
| ExperienceEntry  | `src/content/experience/` | Build-time only | Developer (code)  |
| ContactMessage   | Client memory only        | Ephemeral       | Visitor (runtime) |

No runtime state management library is required. All content is static; interactive
state (form fields, nav highlight) is managed with minimal vanilla JS / Astro client
scripts.
