# Tasks: Portfolio Website

**Input**: Design documents from `specs/001-portfolio-website/`

**Prerequisites**: plan.md ✅, spec.md ✅, research.md ✅, data-model.md ✅, contracts/ ✅

**Branch**: `001-portfolio-website`

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies on incomplete tasks)
- **[Story]**: User story this task belongs to (US1–US6)
- File paths are relative to the repository root

---

## Phase 1: Setup

**Purpose**: Scaffold the project with all tooling configured and ready for development

- [ ] T001 Scaffold Astro 6.3 project with pnpm at repo root; configure `astro.config.ts` with `output: "static"` and `site` placeholder
- [ ] T002 [P] Install and configure TailwindCSS 4.3 — `pnpm add -D @tailwindcss/vite`; register `tailwindcss()` Vite plugin from `@tailwindcss/vite` in `astro.config.ts`; create `src/styles/global.css` with `@import "tailwindcss"` (note: TailwindCSS 4.x uses `@tailwindcss/vite`, NOT the legacy `@astrojs/tailwind` integration)
- [ ] T003 [P] Install Motion 12.x (`pnpm add motion`) — no config needed
- [ ] T004 [P] Configure TypeScript strict mode in `tsconfig.json` — `"strict": true`, `"noUncheckedIndexedAccess": true`
- [ ] T005 [P] Install and configure ESLint (`eslint-config-astro`) + Prettier — create `.eslintrc.cjs` and `.prettierrc`; verify `pnpm eslint src --max-warnings 0` passes on empty project; install `husky` + `commitlint` (`@commitlint/cli`, `@commitlint/config-conventional`) and configure a `commit-msg` hook to enforce Conventional Commits (constitution Principle IV)
- [ ] T006 [P] Install Playwright and initialise e2e test config — `pnpm create playwright`, output `playwright.config.ts`, create `tests/e2e/` directory
- [ ] T007 Create `public/_headers` with baseline security headers: `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`, `Strict-Transport-Security`; set `script-src 'self' 'unsafe-inline'` and `connect-src 'self'` as permissive dev values to avoid blocking local development (GA4 and form-service domains are added and `connect-src` is hardened in T057)

**Checkpoint**: `pnpm dev` starts without errors; `pnpm build` produces a `dist/` folder

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Design system, content schemas, and layout shell that ALL user stories depend on

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T008 **Decision**: Select display, sans, and mono typefaces — research options (e.g. Geist, Inter, Commit Mono, Neue Haas Grotesk, Space Grotesk), download WOFF2 variable font files to `public/fonts/`; confirm licensing
- [ ] T009 **Decision**: Choose accent hue angle — replace `[HUE]` placeholder in design tokens with chosen value (e.g. `210` blue, `270` violet, `160` emerald)
- [ ] T010 Create `src/styles/tokens.css` — all design tokens inside `@theme {}`: colour primitives + semantic aliases (filled accent hue from T009), typography families (filled from T008), size/weight scale, shape/shadow tokens, motion easing and duration tokens; exactly as specified in plan.md Design System section
- [ ] T011 Create `src/styles/global.css` — `@import "./tokens.css"` at top, `@layer base` reset (box-sizing, margin, body background/colour/font using semantic tokens), `@media (prefers-reduced-motion: reduce)` global motion kill-switch
- [ ] T012 Define all four Astro Content Collection schemas in `src/content/config.ts` — `DeveloperProfile`, `Project`, `SkillCategory`, `ExperienceEntry` using Zod as specified in `data-model.md`; build must pass with zero type errors
- [ ] T013 [P] Create placeholder `src/content/profile/index.json` — all required fields, placeholder values (build-time schema validation must pass)
- [ ] T014 [P] Create 3 placeholder `src/content/projects/[slug].json` files — all required fields, placeholder values
- [ ] T015 [P] Create 3 placeholder `src/content/skills/[category-slug].json` files — all required fields with `expert`/`proficient`/`familiar` proficiency tiers
- [ ] T016 [P] Create 2 placeholder `src/content/experience/[slug].json` files — all required fields, placeholder values
- [ ] T017 Create `src/layouts/BaseLayout.astro` — HTML shell with `<html lang="en">`, `<head>` with `<slot name="seo">`, global style import (`global.css`), `<link rel="preload">` for each WOFF2 font with `font-display: swap`, `<slot />` for body content

**Checkpoint**: `pnpm astro check` exits clean; `pnpm build` succeeds with all placeholder content

---

## Phase 3: User Story 1 — Unforgettable First Impression (Priority: P1) 🎯 MVP

**Goal**: A fully rendered, visually striking hero section that owns the full viewport — developer identity is immediately clear and a bespoke animated backdrop commands attention

**Independent Test**: Deploy with only the hero section visible. Show to someone unfamiliar with the site. Their first reaction must include unprompted positive language about the visual design within 3 seconds

- [ ] T018 [US1] Create `src/components/ui/SEO.astro` — accepts `PageSEO` props (title, description, canonicalURL, ogImage, ogType), renders `<title>`, `<meta>` description, canonical, Open Graph, and Twitter Card tags as specified in `contracts/content-contracts.md`
- [ ] T019 [US1] Create `src/components/ui/Button.astro` — primary and ghost variants; all interactive states designed (hover: scale + glow, focus: visible ring using `--color-accent`, active: slight compress, disabled: muted); uses semantic design tokens only
- [ ] T020 [US1] Create `src/components/ui/NavBar.astro` — persistent top bar, transparent when over hero, opaque (`--color-surface`) on scroll (IntersectionObserver or scroll event), section anchor links (Projects, Experience, Skills, Contact); mobile: hamburger icon → full-screen overlay with Motion.js open/close animation
- [ ] T021 [US1] Create `src/components/ui/Footer.astro` — social/profile links from `DeveloperProfile.links` (GitHub, LinkedIn etc.), minimal layout using `--color-muted` text; no heavy borders
- [ ] T022 [US1] Create `src/components/sections/HeroSection.astro` — full-viewport layout (`min-height: 100dvh`): display name as large-scale `--font-display` lockup (a visual element in its own right), professional title, tagline, visual-only scroll cue (no "scroll down" text); NO avatar/stock imagery in hero; layout should feel custom-drawn with deliberate asymmetry
- [ ] T023 [US1] Implement hero bespoke animated backdrop in `src/components/sections/HeroSection.astro` — choose one: cursor-reactive SVG mesh gradient (CSS `radial-gradient` shifted via `mousemove`) OR Motion.js-driven abstract shape animation; must feel premium, not distracting
- [ ] T024 [US1] Add `prefers-reduced-motion` static fallback for all hero motion in `src/components/sections/HeroSection.astro` — full content remains visible and meaningful; no content hidden or broken without animation
- [ ] T025 [US1] Compose `src/pages/index.astro` — wire `BaseLayout` + `SEO` + `NavBar` + `HeroSection` + `Footer`; read `DeveloperProfile` from Content Collection for name/title/tagline; hero fills full viewport with no scroll in initial state
- [ ] T026 [US1] Create `public/og-image.png` — 1200×630 social preview image; developer name + title; dark branded treatment consistent with site aesthetic

**Checkpoint**: Hero section fully functional and visually striking — constitution principle I independently verifiable. NavBar scrolls correctly. `pnpm build` clean.

---

## Phase 4: User Story 2 — Projects Showcase (Priority: P2)

**Goal**: A curated projects section where each entry is visually distinct and communicates what was built, what skills it demonstrates, and how to explore it further

**Independent Test**: Populate with 2 real projects. Ask someone unfamiliar with the developer to describe what each project does and what skills it demonstrates — without seeing any code

- [ ] T027 [P] [US2] Create `src/components/ui/ProjectCard.astro` — name, short description, `tags` as styled badges, project visual (`<Image>` with AVIF/WebP), live URL and source URL buttons (conditional on availability); all interactive states: hover (card lift + glow using `--shadow-glow`), focus (visible ring), active; visually distinct from all other card components on the page
- [ ] T028 [US2] Implement `src/components/sections/ProjectsSection.astro` — asymmetric/staggered layout (not a uniform grid): featured projects get larger treatment, standard projects compact; `inView()` scroll-triggered stagger reveal using Motion.js (`translate-Y` + opacity, spring easing, `--duration-slow`); each project visually distinct (colour accent, graphic treatment)
- [ ] T029 [US2] Add `ProjectsSection` to `src/pages/index.astro` after `HeroSection` (canonical order position 2); wire to Content Collection query (`getCollection('projects')`, sorted by `order`)
- [ ] T030 [US2] Populate `src/content/projects/` with 3+ real project entries — replace placeholder JSON files; ensure `featured: true` on 1–2 entries; real images added to `public/images/projects/`

**Checkpoint**: Projects section renders with real data, visually distinctive card treatment, and scroll-triggered animations. Layout holds with 3–6 entries.

---

## Phase 5: User Story 3 — Skills & Expertise Display (Priority: P3)

**Goal**: A skills section that communicates technical breadth and depth at a glance — grouped by category with clear proficiency differentiation, without resorting to percentage bars

**Independent Test**: Render with real skill data. Ask a non-technical person to identify the developer's top three expertise areas in under 30 seconds

- [ ] T031 [P] [US3] Create `src/components/ui/SkillBadge.astro` — skill name with proficiency tier visual treatment: `expert` tier gets larger size or stronger accent colour/weight; `proficient` standard; `familiar` muted; no numbers or bars; accessible (tier conveyed via `aria-label` in addition to visual)
- [ ] T032 [US3] Implement `src/components/sections/SkillsSection.astro` — skill categories as visual anchors/headings; badges within each category; `inView()` scroll-triggered reveal per category; responsive layout (all skills legible at 375px, no truncation or overlap)
- [ ] T033 [US3] Add `SkillsSection` to `src/pages/index.astro` at canonical position 4 (Hero → Projects → Experience → **Skills** → Contact); append after `ProjectsSection` for now — `ExperienceSection` does not yet exist and will be inserted between them when T037 completes; wire to Content Collection query (`getCollection('skills')`)
- [ ] T034 [US3] Populate `src/content/skills/` with real skill categories and proficiency tiers — replace placeholder JSON files (e.g. Frontend, Backend, DevOps, Tooling categories)

**Checkpoint**: Skills section renders with real data. Category grouping is clear. Expert-tier skills are visually prominent without numbers.

---

## Phase 6: User Story 4 — Experience & Career Timeline (Priority: P4)

**Goal**: A premium-résumé-quality experience section that communicates career progression, seniority, and accomplishments in a scannable format

**Independent Test**: Render with 2–3 real entries. A recruiter must be able to determine seniority level and primary domain without reading the projects section

- [ ] T035 [P] [US4] Create `src/components/ui/ExperienceCard.astro` — role title, company name, formatted duration (human-readable: "2 yrs 3 mos"), start/end dates, 2–4 accomplishment bullet points; premium résumé aesthetic — hierarchy through typography/spacing, not heavy borders; all interactive/focus states
- [ ] T036 [US4] Implement `src/components/sections/ExperienceSection.astro` — chronological (most recent first), timeline or structured list rhythm; `inView()` scroll-triggered reveals per card; spacing and typographic rhythm communicate progression clearly
- [ ] T037 [US4] Add `ExperienceSection` to `src/pages/index.astro` after `ProjectsSection` (canonical order position 3 — Hero → Projects → **Experience** → Skills → Contact); wire to Content Collection query (`getCollection('experience')`, sorted by start date descending)
- [ ] T038 [US4] Populate `src/content/experience/` with 2+ real career entries — replace placeholder JSON files

**Checkpoint**: Experience section renders with real data. Career progression is visually clear. Layout adapts to long role titles or multi-year entries.

---

## Phase 7: User Story 5 — Contact & Engagement (Priority: P5)

**Goal**: A polished, low-friction contact section with a working form submission, inline validation, and a direct email fallback

**Independent Test**: Complete the contact flow end-to-end — submit a test message and verify receipt — with no other section present

- [ ] T039 **Decision**: Select contact form provider (Formspree / Web3Forms / Getform) — evaluate against constraints (no extra JS bundle, host-agnostic, native honeypot, free tier ≤ 50 submissions/month); register account; obtain endpoint URL
- [ ] T040 [US5] Create `src/components/ui/ContactForm.astro` — fields: name, email, message; hidden honeypot field (`tabindex="-1"`, `aria-hidden="true"`); inline HTML5 + JS validation (email format, required fields — no network request if invalid); loading state (button spinner/disabled); success state (confirmation message, form reset); error state (human-friendly message, data preserved)
- [ ] T041 [US5] Wire `ContactForm.astro` to chosen provider endpoint from T039 — `fetch` POST with `Content-Type: application/json`; honeypot field excluded from submission payload; handle provider error responses gracefully
- [ ] T042 [US5] Implement `src/components/sections/ContactSection.astro` — large inviting heading, `ContactForm`, direct email `mailto:` link from `DeveloperProfile.email` as fallback, social profile links; generous touch targets (min 44×44px)
- [ ] T043 [US5] Add `ContactSection` to `src/pages/index.astro` as the final section (canonical position 5); confirm end-to-end form submission in browser

**Checkpoint**: Contact form submits successfully to provider. Validation catches bad email before submission. Success/error states render correctly.

---

## Phase 8: User Story 6 — Performance & Accessibility (Priority: P6)

**Goal**: Lighthouse Performance ≥ 95, Accessibility = 100, zero WCAG 2.2 AA violations, full keyboard navigation, correct behaviour at 375px

**Independent Test**: Lighthouse audit (mobile, simulated slow-4G) passes all thresholds. `pnpm exec playwright test` — all tests pass

- [ ] T044 [P] [US6] Write `tests/e2e/navigation.spec.ts` — section anchor links scroll to correct sections; NavBar is transparent over hero and opaque after scroll; mobile overlay opens/closes correctly; all links are keyboard reachable
- [ ] T045 [P] [US6] Write `tests/e2e/accessibility.spec.ts` — axe-core audit via `@axe-core/playwright` (zero WCAG 2.2 AA violations); all landmark roles present (`main`, `nav`, `footer`); all images have non-empty `alt`; tab order follows visual reading order
- [ ] T046 [P] [US6] Write `tests/e2e/mobile-viewport.spec.ts` — viewport 375px wide; no horizontal scroll (`document.body.scrollWidth <= 375`); all interactive elements have min 44×44px tap targets; all text is legible (no overflow/truncation)
- [ ] T047 [US6] Write `tests/e2e/contact-form.spec.ts` — submit with invalid email and verify inline error; submit valid form and verify success state; verify honeypot field is hidden from assistive technology
- [ ] T048 [US6] Audit all interactive states across every component (hover, focus, active, disabled, loading) — fix any element remaining in browser-default state; every control must have a visible focus indicator (VQ-003)
- [ ] T049 [US6] Optimise all images — ensure project visuals and avatar are served via Astro `<Image>` component with `format="avif"` fallback `webp`; verify total page weight ≤ 1.5 MB uncompressed
- [ ] T050 [US6] Run `pnpm exec playwright test` — fix all failing tests (navigate, a11y, mobile, contact-form)
- [ ] T051 [US6] Run Lighthouse audit (Chrome DevTools, Mobile, simulated slow-4G) — resolve any metric below threshold: Performance ≥ 95, Accessibility = 100, Best Practices ≥ 95; common fixes: image sizing, font preload, render-blocking resources

**Checkpoint**: All four Playwright test suites pass. Lighthouse Performance ≥ 95, Accessibility = 100. No horizontal overflow at 375px.

---

## Phase 9: Polish & Launch

**Purpose**: Cross-cutting quality gates, real content, GA4 integration, and Cloudflare Pages deployment

- [ ] T052 [P] Replace all remaining placeholder content with real data — `src/content/profile/index.json`, all project/skill/experience JSON files; replace placeholder images in `public/`
- [ ] T053 [P] Run `pnpm astro check` + `pnpm eslint src --max-warnings 0` — fix all type errors, lint warnings, and formatting issues
- [ ] T054 Refine all Motion.js animation curves, stagger timings, and durations across all sections — verify VQ-004 (feel handcrafted, not preset); revisit hero backdrop, section reveals, card hover states
- [ ] T055 [P] Cross-browser visual check — Chrome, Firefox, Safari (macOS); fix any rendering inconsistencies in blend modes, backdrop-filter, OKLCH colours, or font rendering
- [ ] T056 Typography and spacing audit — confirm no browser default values remain anywhere; all text uses `--font-*` tokens, all spacing uses token-derived values (VQ-002)
- [ ] T057 Integrate GA4 in `src/layouts/BaseLayout.astro` — async `gtag.js` script with `PUBLIC_GA_ID` env var guard exactly as specified in plan.md; update `public/_headers` CSP `script-src` and `connect-src` with GA4 domains; replace `[form-service-domain]` placeholder in `connect-src` with actual contact form provider domain
- [ ] T058 Configure Cloudflare Pages project — connect GitHub repo, set build command (`pnpm build`), output directory (`dist`), Node.js version (`20`), add `PUBLIC_GA_ID` environment variable (Measurement ID — NOT committed to repo); update `site` in `astro.config.ts` to the production URL (required for correct canonical URLs and OG tags)
- [ ] T059 Deploy to Cloudflare Pages — verify production URL; run quickstart.md validation checklist end-to-end; confirm GA4 fires on live site; confirm contact form submits successfully on production
- [ ] T060 Share deployed site with 3+ independent reviewers unfamiliar with the developer; collect first-reaction feedback; confirm design is assessed as "distinctive and memorable" — not template-like or generic (SC-008); resolve any critical visual feedback before marking the feature complete

**Checkpoint**: Site is live on Cloudflare Pages. All validation checklist items checked. GA4 verified in GA Realtime view. SC-008 reviewer sign-off obtained.

---

## Dependencies & Execution Order

### Phase Dependencies

```
Phase 1 (Setup)         → no dependencies, start immediately
Phase 2 (Foundational)  → depends on Phase 1 completion — BLOCKS all user story phases
Phase 3 (US1 Hero)      → depends on Phase 2 — MVP milestone
Phase 4 (US2 Projects)  → depends on Phase 2 — can start in parallel with Phase 3
Phase 5 (US3 Skills)    → depends on Phase 2 — can start after Phase 2
Phase 6 (US4 Experience)→ depends on Phase 2 — can start after Phase 2
Phase 7 (US5 Contact)   → depends on Phase 2 + T039 decision
Phase 8 (US6 Perf/A11y) → depends on all content sections being present (Phases 3–7)
Phase 9 (Polish/Launch) → depends on Phase 8 passing all audits
```

### User Story Dependencies

| Story | Depends On                       | Notes                                          |
| ----- | -------------------------------- | ---------------------------------------------- |
| US1   | Phase 2 (Foundational)           | MVP deliverable — highest value, do first      |
| US2   | Phase 2                          | Independent — parallelize with US1 if possible |
| US3   | Phase 2                          | Independent of US1/US2                         |
| US4   | Phase 2                          | Independent of US1/US2/US3                     |
| US5   | Phase 2 + T039 provider decision | Start T039 early to avoid blockage             |
| US6   | US1–US5 all present              | Tests need full site to be meaningful          |

### Key Decision Points (Not to be skipped)

- **T008**: Typeface selection — blocks T010, T017 (font preloading), and all visual rendering
- **T009**: Accent hue selection — blocks T010 (tokens.css) and all coloured UI
- **T039**: Contact form provider — blocks T040–T043

### Parallel Opportunities

**Within Phase 1** (all can run simultaneously after T001):
T002, T003, T004, T005, T006

**Within Phase 2** (after T010 + T012 complete):
T013, T014, T015, T016 — all can run in parallel

**Within Phase 3 (US1)** (after T017 BaseLayout):
T018, T019, T020, T021 — UI primitives can be built in parallel

**Across Phases 3–7** (after Phase 2 complete):
US2, US3, US4 can all be started without waiting for each other or for US1 to finish

**Within Phase 8**:
T044, T045, T046 — test files can be authored in parallel

---

## Implementation Strategy

### MVP Scope (minimum to validate the concept)

Complete **Phase 1 + Phase 2 + Phase 3 (US1)** first. At that point:

- The site is deployed and functional
- The hero delivers the primary "WOW" moment
- The design system and content pipeline are proven
- All subsequent sections follow the same pattern

### Incremental Delivery Order (recommended)

1. Phase 1–2: Scaffold + design system (T001–T017)
2. Phase 3: Hero (T018–T026) → **deploy preview, validate constitution principle I**
3. Phase 4: Projects (T027–T030) → deploy, review card layouts with real data
4. Phase 6: Experience (T035–T038) → deploy (note: before Skills in page order)
5. Phase 5: Skills (T031–T034) → deploy
6. Phase 7: Contact (T039–T043) → deploy, test form end-to-end
7. Phase 8: Perf/A11y (T044–T051) → run audits, fix issues
8. Phase 9: Polish + GA4 + Launch (T052–T059)

---

## Summary

| Metric                       | Value                   |
| ---------------------------- | ----------------------- |
| Total tasks                  | 60                      |
| Setup tasks (Phase 1)        | 7                       |
| Foundational tasks (Phase 2) | 10                      |
| US1 — Hero tasks             | 9                       |
| US2 — Projects tasks         | 4                       |
| US3 — Skills tasks           | 4                       |
| US4 — Experience tasks       | 4                       |
| US5 — Contact tasks          | 5                       |
| US6 — Performance tasks      | 8                       |
| Polish & Launch tasks        | 9                       |
| Parallelizable tasks [P]     | 26                      |
| Decision points              | 3 (T008, T009, T039)    |
| MVP milestone                | T025 (Phase 3 complete) |
