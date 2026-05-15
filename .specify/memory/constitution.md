<!--
SYNC IMPACT REPORT
==================
Version change: template → 1.0.0
New sections: Core Principles (I–V), Technology Standards, Development Workflow, Governance
Modified principles: n/a (initial ratification)
Added sections: I. Visual Distinction, II. Cutting-Edge Stack, III. Attention to Detail,
                IV. Code Quality & Standards, V. Simplicity First
Removed sections: none
Templates updated:
  ✅ .specify/memory/constitution.md — this file
  ⚠ .specify/templates/plan-template.md — Constitution Check gates should reference principles I–V
  ⚠ .specify/templates/spec-template.md — visual/UX acceptance criteria should cite Principle I & III
  ⚠ .specify/templates/tasks-template.md — task categories should include visual polish & a11y tasks
Follow-up TODOs: none — all placeholders resolved
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

## Development Workflow

- All features MUST be developed on dedicated branches following the naming convention
  established in `.specify/extensions.yml` (sequential feature branches).
- A feature is not shippable until: TypeScript compiles with zero errors, ESLint reports
  zero warnings, and all existing tests pass.
- Visual changes MUST be reviewed against Principle I (Visual Distinction) and
  Principle III (Attention to Detail) before merge.
- Commit messages MUST follow the Conventional Commits specification.
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

**Version**: 1.0.0 | **Ratified**: 2026-05-15 | **Last Amended**: 2026-05-15
