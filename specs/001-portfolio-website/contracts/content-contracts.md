# Contracts: Portfolio Website

**Feature**: 001-portfolio-website
**Phase**: 1 — Design
**Date**: 2026-05-15

This project exposes three interface contracts:

1. **Content schemas** — the data format that populates the site (build-time)
2. **Contact form submission** — the payload sent to the form service (runtime)
3. **SEO metadata** — the shape of the `<head>` metadata emitted per page (build-time)

---

## Contract 1: Content Collection Schemas

These schemas define the authoritative format for all site content. Any content file
that does not conform to these schemas will cause the build to fail with a descriptive
error.

### Profile Schema

```typescript
// src/content/profile/index.json must match this shape
{
  "name": string,               // Full name, e.g., "Jane Doe"
  "title": string,              // Professional title, e.g., "Senior Frontend Engineer"
  "tagline": string,            // ≤120 chars — positioning statement
  "avatar": string,             // Relative path to image, e.g., "/images/avatar.jpg"
  "email": string,              // Valid email address
  "location"?: string,          // Optional, e.g., "London, UK"
  "links": [
    {
      "label": string,          // e.g., "GitHub"
      "url": string,            // Absolute URL
      "icon": string            // Icon key from icon set
    }
  ]
}
```

### Project Schema

```typescript
// src/content/projects/<slug>.json must match this shape
{
  "title": string,
  "slug": string,               // /^[a-z0-9-]+$/
  "description": string,        // ≤280 chars
  "longDescription"?: string,
  "tags": string[],             // 1–8 items
  "image": string,              // Relative path to project visual
  "imageAlt": string,           // Non-empty description
  "liveUrl"?: string,           // Absolute URL
  "sourceUrl"?: string,         // Absolute URL
  "featured": boolean,          // Default: false
  "order": number,              // Positive integer
  "year": number                // YYYY
}
```

**Invariant**: At least one of `liveUrl` or `sourceUrl` MUST be present for any
project displayed publicly.

### Skill Category Schema

```typescript
// src/content/skills/<category-slug>.json must match this shape
{
  "category": string,           // Human-readable category name
  "slug": string,               // /^[a-z0-9-]+$/
  "order": number,              // Positive integer — display order
  "skills": [
    {
      "name": string,
      "tier": "expert" | "proficient" | "familiar",
      "icon"?: string,
      "order": number
    }
  ]                             // Minimum 1 skill per category
}
```

### Experience Entry Schema

```typescript
// src/content/experience/<slug>.json must match this shape
{
  "role": string,
  "company": string,
  "companyUrl"?: string,        // Absolute URL
  "startDate": string,          // "YYYY-MM" format
  "endDate": string,            // "YYYY-MM" or "present"
  "location"?: string,
  "accomplishments": string[]   // 2–5 items
}
```

---

## Contract 2: Contact Form Submission

**Direction**: Browser → Third-party form service endpoint
**Method**: HTTP POST
**Content-Type**: `application/x-www-form-urlencoded` or `application/json`
(determined by the selected form service — see research.md)

**Request payload**:

```typescript
{
  name: string; // Required — visitor's name; non-empty
  email: string; // Required — valid email; validated before POST
  message: string; // Required — 10–2000 characters
  _honeypot: string; // Always present; MUST be empty string for real submissions
}
```

**Success response**: HTTP 200 or HTTP 302 redirect (provider-dependent)

- On success: UI transitions to a "Thank you" confirmation state
- Form fields are cleared

**Error response**: HTTP 4xx or 5xx, or network failure

- On failure: UI shows an inline error message; form data is preserved
- No retry logic — user may resubmit manually

**Client-side validation rules** (enforced before any network request):

| Field     | Rule                   | Error message                                     |
| --------- | ---------------------- | ------------------------------------------------- |
| `name`    | Non-empty              | "Please enter your name."                         |
| `email`   | RFC 5322 email pattern | "Please enter a valid email address."             |
| `message` | 10–2000 characters     | "Message must be between 10 and 2000 characters." |

**Honeypot behaviour**: The `_honeypot` field is a hidden `<input>` with
`tabindex="-1"` and `aria-hidden="true"`. If the field contains any value when
the form is submitted, the submission is silently discarded client-side (no request
is made). This prevents the vast majority of automated bot submissions.

---

## Contract 3: SEO Metadata

Every page `<head>` MUST include the following metadata, emitted by the `<SEO>`
component:

```typescript
interface PageSEO {
  // Standard
  title: string; // "{name} — {title}" format
  description: string; // ≤160 characters
  canonical: string; // Absolute URL of the page

  // Open Graph
  'og:title': string; // Same as title
  'og:description': string; // Same as description
  'og:url': string; // Same as canonical
  'og:type': 'website';
  'og:image': string; // Absolute URL to 1200×630 OG image
  'og:image:width': '1200';
  'og:image:height': '630';
  'og:site_name': string; // Developer's name

  // Twitter Card
  'twitter:card': 'summary_large_image';
  'twitter:title': string;
  'twitter:description': string;
  'twitter:image': string;
}
```

**OG Image**: A single static 1200×630 PNG at `public/og-image.png`.
Generated once, not dynamic. Should visually reflect the portfolio brand.

---

## Contract Versioning

These contracts are internal to the repository. Breaking changes (e.g., renaming a
required field, changing an enum value) require:

1. Updating all content files in `src/content/` to conform
2. Updating the schema in `src/content/config.ts`
3. Updating this contracts document
4. Verifying the build passes with zero type errors

No external consumers depend on these contracts; versioning is for internal consistency only.
