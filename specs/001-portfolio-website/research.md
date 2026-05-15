# Research: Portfolio Website

**Feature**: 001-portfolio-website
**Phase**: 0 — Outline & Research
**Date**: 2026-05-15

---

## Framework Selection

### Decision: Astro 6.3

**Rationale**:
Astro 6.3 is the optimal framework for a performance-first, content-driven portfolio site.
Its island architecture ships zero JavaScript to the browser by default, making Lighthouse
Performance ≥95 achievable without heroic optimisation effort. Content Collections provide
type-safe, Zod-validated schemas for projects, skills, and experience data — eliminating
runtime data-shaping bugs. Built-in View Transitions API support enables stunning
page-morph animations with a single directive. The `.astro` component format is clean,
readable TypeScript-first HTML with co-located styles.

Critically, choosing Astro over Angular demonstrates framework versatility to
technical recruiters — a direct signal of engineering breadth.

**Current stable version**: 6.3 (May 2026)

**Alternatives considered**:

| Framework           | Verdict | Reason rejected                                                          |
| ------------------- | ------- | ------------------------------------------------------------------------ |
| Next.js 15          | ❌      | React overhead; SSR-default adds complexity for a fully-static portfolio |
| SvelteKit 2         | 🟡      | Excellent DX and Svelte 5 runes are genuinely elegant; viable runner-up  |
| Nuxt 4              | ❌      | Vue ecosystem; good but less widespread recognition among recruiters     |
| Vite + React 19 raw | ❌      | No opinions or structure; requires assembling all tooling manually       |
| Angular 20          | ❌      | Explicitly excluded by user — portfolio should demonstrate versatility   |

---

## Styling

### Decision: TailwindCSS 4.3

**Rationale**:
TailwindCSS 4.3 introduces a CSS-first configuration model via `@theme {}` — no
`tailwind.config.js` file is required. The new Oxide engine (Rust-based) is 5× faster
than v3. Design tokens defined as CSS custom properties inside `@theme {}` integrate
seamlessly with Motion's JS animations. OKLCH P3 colour gamut support enables more
vibrant, distinctive colour choices than sRGB-limited palettes. The zero-unused-CSS
output guarantee keeps the stylesheet under 10 kB.

**Current stable version**: 4.3 (May 2026)

**Key v4 capabilities used**:

- `@theme {}` block for all design tokens (colours, spacing, typography, easing)
- OKLCH colour palette for wider P3 gamut
- CSS cascade layers (`@layer base`, `@layer components`, `@layer utilities`)
- Container queries (`@container`)
- 3D transform utilities (`perspective-*`, `rotate-x-*`, `rotate-y-*`)

**Alternatives considered**:

- CSS Modules — verbose for utility-heavy UI, no design token system
- Vanilla CSS with custom properties only — viable for small sites, lacks responsive
  utilities; rejected on productivity grounds

---

## Animation Library

### Decision: Motion 12.38 (vanilla JS)

**Rationale**:
Motion 12.38 is the highest-performance JS animation library available, with 30 million
monthly npm downloads. Its vanilla JS API (`animate`, `scroll`, `inView`, `stagger`) is
framework-agnostic and integrates cleanly with Astro's partial hydration model — no
React wrapper is needed. Spring physics produces natural, non-mechanical motion.
`inView()` scroll-triggered animations are hardware-accelerated via the Web Animations
API (WAAPI). The footprint is tiny and tree-shakeable.

**Current stable version**: 12.38.0 (Motion) / 5.4.3 (Motion for React — unused here)

**Key APIs selected**:

- `animate(element, keyframes, options)` — element-level animations
- `scroll(animate(...), { target })` — scroll-linked progress
- `inView(element, callback)` — intersection-triggered enter animations
- `stagger(delay)` — cascade timing for lists and grids
- Spring easing: `{ type: "spring", stiffness: 300, damping: 25 }` for organic feel

**Alternatives considered**:

- GSAP 3 — more powerful for timeline-heavy sequences; overkill for a portfolio and
  requires licence for commercial use (ScrollTrigger plugin)
- CSS animations only — insufficient control for complex scroll-triggered reveals and
  spring physics; no JS-driven interaction possible
- AOS (Animate on Scroll) — unmaintained; produces generic "scroll reveal" feel that
  violates Constitution Principle I (Visual Distinction)

---

## Hosting Platform

### Decision: Cloudflare Pages ⭐

**Rationale**:
For a static Astro build, Cloudflare Pages delivers the fastest global Time-to-First-Byte
(TTFB) of any hosting provider. Its CDN spans 270+ Points of Presence (PoPs) globally,
compared to ~100 for Vercel and ~30 for Netlify. The free tier includes unlimited
bandwidth and 500 builds/month — no cost for a personal portfolio. The `_headers` file
mechanism allows fine-grained CSP, HSTS, and caching header control without a server.
Cloudflare Workers are available if a serverless form proxy is ever needed.

**Free tier limits**: 500 builds/month, unlimited bandwidth, unlimited requests.

**Alternatives considered**:

| Platform             | Pros                                                       | Cons                                                            | Verdict                      |
| -------------------- | ---------------------------------------------------------- | --------------------------------------------------------------- | ---------------------------- |
| **Cloudflare Pages** | 270+ PoPs, unlimited bandwidth, Workers, CSP headers       | Slightly less polished DX than Vercel                           | ⭐ Recommended               |
| **Vercel**           | Best DX, 100+ PoPs, preview deployments, Astro integration | 100 GB/month bandwidth limit on free tier; less global coverage | Runner-up                    |
| **Netlify**          | Established, good Astro support                            | Slower build times, fewer edge PoPs than Cloudflare             | Acceptable                   |
| **GitHub Pages**     | Free, zero-config for public repos                         | No custom headers, no edge functions, weak CDN                  | ❌ Rejected — no CSP control |

**Deployment config**: `astro.config.ts` with `output: "static"` + `wrangler.toml` for
Cloudflare Pages settings + `public/_headers` for HTTP security headers.

---

## Contact Form Service

### Decision: TBD (deferred — constraints specified)

From spec clarification Q3, the provider is deferred to implementation. The chosen service
MUST satisfy:

1. No additional JS bundle added to the page (plain `fetch` POST or `action` attribute)
2. Host-agnostic (not tied to Netlify/Vercel)
3. Native honeypot field support
4. Free tier sufficient for a personal portfolio (< 50 submissions/month expected)

**Likely candidates at implementation time**: Formspree, Web3Forms, Getform.

---

## TypeScript Configuration

### Decision: TypeScript 5.x, strict mode

`tsconfig.json` extends Astro's recommended config with `strict: true`. All content
collection schemas are defined using Zod (bundled with Astro — no additional dependency).
Zero type errors enforced in CI.

---

## Testing Strategy

### Decision: Playwright only (e2e critical paths)

Per Constitution Principle V (Simplicity First): a static portfolio has no business
logic to unit-test. Playwright covers the scenarios that matter:

- Contact form submit → success/error state
- Navigation anchors reach correct sections
- Reduced-motion check (via `--force-prefers-reduced-motion`)
- Mobile viewport rendering at 375px

No Vitest / Jest setup. No storybook. No visual regression (manual review via
Cloudflare preview deployments instead).

---

## Font Strategy

### Decision: Variable fonts, self-hosted

To eliminate render-blocking Google Fonts requests and avoid FOIT (Flash of Invisible Text):

- All fonts self-hosted in `public/fonts/` as WOFF2
- `font-display: swap` for immediate text visibility
- `<link rel="preload">` in `<head>` for the primary display font
- CSS `font-face` declarations in global stylesheet

Specific typefaces: TBD at design phase. Candidates: Geist (sans-serif variable),
Cabinet Grotesk, Mona Sans, or a geometric display font for headings + a mono for accents.

---

## Image Strategy

### Decision: Astro's built-in `<Image>` component + AVIF/WebP

- `astro:assets` `<Image>` component generates optimised AVIF/WebP with `srcset`
- All project screenshots and avatar processed at build time
- Explicit `width`, `height`, and `alt` required (enforced by Astro type-check)
- No lazy-loading for above-the-fold hero image; `loading="lazy"` for all others
- Open Graph image: single 1200×630 static PNG in `public/`

---

## SEO Metadata

### Decision: Full social metadata (per spec clarification Q2)

Implemented via a reusable `<SEO>` Astro component included in the page `<head>`:

- `<title>`, `<meta name="description">`
- `<link rel="canonical">`
- Open Graph: `og:title`, `og:description`, `og:image`, `og:url`, `og:type`
- Twitter Card: `twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`
- No JSON-LD structured data (explicitly out of scope per spec clarification Q2)

---

## Security Headers

Delivered via `public/_headers` on Cloudflare Pages:

```
Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self' https://api.formspree.io; frame-ancestors 'none'
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

Note: `connect-src` domain updated once contact form provider is confirmed.

---

## Resolved Clarifications

All NEEDS CLARIFICATION items from Technical Context resolved:

| Item                  | Resolution                                |
| --------------------- | ----------------------------------------- |
| Framework             | Astro 6.3                                 |
| Styling               | TailwindCSS 4.3                           |
| Animations            | Motion 12.38                              |
| Hosting               | Cloudflare Pages                          |
| Contact form provider | TBD — constraints locked                  |
| TypeScript config     | Strict mode, Astro tsconfig preset        |
| Testing               | Playwright e2e only                       |
| Font strategy         | Self-hosted WOFF2 variable fonts          |
| Image strategy        | Astro `<Image>` + AVIF/WebP at build time |
| SEO metadata          | OG + Twitter Card; no JSON-LD             |
