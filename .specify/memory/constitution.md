<!--
SYNC IMPACT REPORT
==================
Version change: 1.0.0 → 1.1.0 (MINOR — Technology Standards updated)
Amendment: Replaced Angular-specific Technology Standards with Astro-based stack
Modified sections: Technology Standards
Modified principles: II (non-Angular framing already present; no text change needed)
Added: Astro 6.x, TailwindCSS 4.x, Motion 12.x, Playwright, eslint-config-astro
Removed: Angular CLI, angular-eslint, Angular Animations API, Angular Testing Library
Templates updated:
  ✅ .specify/memory/constitution.md — this file
  ✅ .specify/templates/plan-template.md — Constitution Check gates already reflect I–V
  (no further template changes required for this amendment)
Follow-up TODOs: none
-->

# Portfolio v2 Constitution

## Core Principles

### I. Visual Distinction (NON-NEGOTIABLE)

Every UI element, layout, and interaction MUST be striking, memorable, and unmistakably
non-generic. Cookie-cutter patterns, AI slop aesthetics, and off-the-shelf template designs
are FORBIDDEN. Designs MUST demonstrate creative intent in composition, typography, motion,
and color. When in doubt, go bolder — safe is invisible.

**Rationale**: A personal portfolio's sole job is to make an impression. Blending in is
failure. Every visitor MUST remember what they saw.

### II. Cutting-Edge, Stable Stack

The latest stable release of every dependency MUST be used at time of feature development.
Deprecated APIs, legacy syntax, and outdated patterns are FORBIDDEN. Prefer a non-Angular
framework to demonstrate diversification and versatility.

**Rationale**: The portfolio is also a demonstration of technical currency. Using outdated
tools contradicts the message.

### III. Attention to Detail

Every spacing value, animation curve, transition duration, ARIA label, and font weight
MUST be intentional and deliberate. Placeholder content, unrefined layout gaps, default
browser styles, and "good enough" visual polish are FORBIDDEN. Interactions MUST feel
crafted, not assembled.

**Rationale**: Detail is the difference between a side project and professional work.
Visitors notice imprecision even when they cannot name it.

### IV. Code Quality & Standards

TypeScript strict mode MUST be enabled and zero type errors tolerated. ESLint MUST pass
with zero warnings before any commit. SOLID principles MUST guide component and service
design. Dead code, commented-out blocks, and redundant abstractions are FORBIDDEN.
Consistent naming and file organisation conventions MUST be followed throughout.

**Rationale**: Code quality is invisible to visitors but paramount for maintainability
and as a signal of engineering discipline visible in the repository.

### V. Simplicity First

The simplest correct solution MUST be chosen over a clever, over-engineered one. YAGNI
applies — features and abstractions MUST NOT be added speculatively. Verbose code MUST
be refactored to its most concise idiomatic form. One clear, canonical approach per
problem; no parallel patterns serving the same purpose.

**Rationale**: Complexity compounds. Simple code is easy to change, review, and reason
about — which matters most in a fast-moving solo project.

## Technology Standards

The following stack MUST be used. Deviations require explicit justification in the
relevant feature spec.

- **Framework**: Astro 6.x (latest stable) — zero-JS by default, Content Collections,
  View Transitions, island architecture; demonstrates non-Angular versatility
- **Language**: TypeScript 5.x (strict mode, latest stable) — zero type errors enforced
- **Styling**: TailwindCSS 4.x — CSS-first config via `@theme {}`, OKLCH P3 colour palette,
  custom design tokens; no inline `style` attributes
- **Animations**: Motion 12.x (vanilla JS API) — `animate()`, `scroll()`, `inView()`;
  spring physics and custom easing curves preferred over CSS transitions for key interactions
- **Build**: Astro's built-in Vite 6 builder — no custom Vite config unless unavoidable
- **Testing**: Playwright (e2e, critical user journeys only); no unit test framework
  required for a static portfolio (YAGNI)
- **Linting/Formatting**: ESLint (`eslint-config-astro`) + Prettier — zero-warning policy
- **Accessibility**: WCAG 2.2 AA MUST be met for all interactive elements

## Development Workflow

- All features MUST be developed on dedicated branches following the naming convention
  established in `.specify/extensions.yml` (sequential feature branches).
- A feature is not shippable until: TypeScript compiles with zero errors, ESLint reports
  zero warnings, and all existing tests pass.
- Visual changes MUST be reviewed against Principle I (Visual Distinction) and
  Principle III (Attention to Detail) before merge.
- Commit messages MUST follow the Conventional Commits specification.
- Every commit that implements a task MUST reference its GitHub issue using a footer
  token — either `Closes #N` (auto-closes on merge) or `Refs #N` (partial progress).
  Commits with no issue reference are FORBIDDEN except for `chore:` and `docs:` housekeeping.
- No force-pushes to `main`. The `main` branch MUST always be deployable.

## Governance

This constitution supersedes all other practices, conventions, or informal agreements in
this repository. Amendments MUST:

1. Increment the version following semantic versioning (MAJOR: principle removal/redefinition;
   MINOR: new principle or section; PATCH: clarification or wording fix).
2. Update `LAST_AMENDED_DATE` to the date of the change.
3. Propagate relevant changes to all templates in `.specify/templates/`.
4. Be committed with a message of the form:
   `docs: amend constitution to vX.Y.Z (<summary of change>)`

Compliance is verified at every plan and task-generation step via the Constitution Check
gate defined in `.specify/templates/plan-template.md`.

**Version**: 1.1.0 | **Ratified**: 2026-05-15 | **Last Amended**: 2026-05-15
