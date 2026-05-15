# Quickstart: Portfolio Website

**Feature**: 001-portfolio-website
**Date**: 2026-05-15
**Stack**: Astro 6.3 · TypeScript 5.x · TailwindCSS 4.3 · Motion 12.x

---

## Prerequisites

| Tool    | Minimum version | Install             |
| ------- | --------------- | ------------------- |
| Node.js | 20.x LTS        | https://nodejs.org  |
| pnpm    | 9.x             | `npm i -g pnpm`     |
| Git     | any             | https://git-scm.com |

Verify:

```bash
node --version   # v20.x or higher
pnpm --version   # 9.x or higher
```

---

## 1. Create the project

```bash
pnpm create astro@latest portfolio-v2 \
  --template minimal \
  --typescript strict \
  --no-install \
  --no-git

cd portfolio-v2
```

---

## 2. Install dependencies

```bash
pnpm install

# TailwindCSS v4 + Astro integration
pnpm astro add tailwind

# Motion animation library (vanilla JS)
pnpm add motion

# Playwright for e2e tests
pnpm add -D @playwright/test
npx playwright install --with-deps chromium
```

---

## 3. Configure TailwindCSS v4

Replace the generated `src/styles/global.css` with:

```css
@import 'tailwindcss';

@theme {
  /* Typography */
  --font-sans: 'YourSans', system-ui, sans-serif;
  --font-mono: 'YourMono', ui-monospace, monospace;
  --font-display: 'YourDisplay', var(--font-sans);

  /* Colour palette — OKLCH for P3 gamut */
  --color-bg: oklch(0.08 0.01 260);
  --color-surface: oklch(0.12 0.015 260);
  --color-border: oklch(0.2 0.02 260);
  --color-text: oklch(0.95 0.01 260);
  --color-muted: oklch(0.55 0.02 260);
  --color-accent: oklch(0.72 0.2 210); /* Update to chosen accent */

  /* Spacing scale */
  --spacing-section: 8rem;

  /* Custom easing */
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);
  --ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);
}

@layer base {
  :root {
    color-scheme: dark;
  }

  body {
    background-color: var(--color-bg);
    color: var(--color-text);
    font-family: var(--font-sans);
    -webkit-font-smoothing: antialiased;
  }

  :focus-visible {
    outline: 2px solid var(--color-accent);
    outline-offset: 3px;
    border-radius: 3px;
  }

  @media (prefers-reduced-motion: reduce) {
    *,
    *::before,
    *::after {
      animation-duration: 0.01ms !important;
      animation-iteration-count: 1 !important;
      transition-duration: 0.01ms !important;
    }
  }
}
```

---

## 4. Configure Astro

```typescript
// astro.config.ts
import {defineConfig} from 'astro/config';
import tailwind from '@astrojs/tailwind';

export default defineConfig({
  site: 'https://your-domain.com', // Update before deploy
  integrations: [tailwind()],
  output: 'static',
  image: {
    formats: ['avif', 'webp'],
  },
});
```

---

## 5. Set up Content Collections

```bash
mkdir -p src/content/{profile,projects,skills,experience}
touch src/content/config.ts
```

Paste the schema definitions from [data-model.md](../data-model.md) into
`src/content/config.ts`. Add at least one sample entry in each collection to
verify the build.

---

## 6. Project structure

```text
src/
├── components/
│   ├── sections/       # HeroSection, ProjectsSection, ExperienceSection, SkillsSection, ContactSection
│   ├── ui/             # Button, Tag, Card, NavBar, SEO, etc.
│   └── icons/          # SVG icon components
├── content/
│   ├── config.ts       # Collection schemas
│   ├── profile/
│   ├── projects/
│   ├── skills/
│   └── experience/
├── layouts/
│   └── BaseLayout.astro
├── pages/
│   └── index.astro     # Single page — all sections composed here
└── styles/
    └── global.css      # Tailwind @theme + base styles

public/
├── fonts/              # Self-hosted WOFF2 variable fonts
├── images/             # Avatar, project screenshots
└── og-image.png        # 1200×630 Open Graph image
```

---

## 7. Start development server

```bash
pnpm dev
```

Open http://localhost:4321

---

## 8. Run type-checking and linting

```bash
pnpm astro check           # TypeScript type-check (zero errors required)
pnpm eslint src --max-warnings 0   # ESLint (zero warnings required)
```

---

## 9. Run e2e tests

```bash
pnpm exec playwright test
```

---

## 10. Build for production

```bash
pnpm build
pnpm preview              # Preview the production build locally
```

The `dist/` directory contains the fully static output ready for deployment.

---

## 11. Deploy to Cloudflare Pages

### Option A — Cloudflare Dashboard (first-time setup)

1. Push repository to GitHub
2. Log in to https://dash.cloudflare.com → **Pages** → **Create a project**
3. Connect GitHub repo
4. Build settings:
   - **Build command**: `pnpm build`
   - **Build output directory**: `dist`
   - **Node.js version**: `20`
5. Click **Deploy**

### Option B — Wrangler CLI

```bash
pnpm add -D wrangler
pnpm exec wrangler pages deploy dist --project-name portfolio-v2
```

### Security headers

Create `public/_headers`:

```
/*
  Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self' https://formspree.io; frame-ancestors 'none'
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Permissions-Policy: camera=(), microphone=(), geolocation=()
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

Update `connect-src` with the actual form service domain once selected.

---

## Validation checklist

Before any public deployment:

- [ ] `pnpm astro check` exits with 0 errors
- [ ] `pnpm eslint src --max-warnings 0` exits clean
- [ ] `pnpm build` succeeds
- [ ] `pnpm exec playwright test` — all tests pass
- [ ] Lighthouse Performance ≥ 95 (run via Chrome DevTools → Lighthouse)
- [ ] Lighthouse Accessibility = 100
- [ ] All placeholder content replaced with real content
- [ ] OG image present at `/og-image.png` (1200×630)
- [ ] `public/_headers` contains all security headers
- [ ] `astro.config.ts` `site` property set to production URL
