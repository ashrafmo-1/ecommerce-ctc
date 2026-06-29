# Project Agent Guide

This file is the base project entry point for agent work in the `ecommerce-ctc-analyses` Nx workspace.

For full role, skill, architecture, design, and task-template guidance, use:

- `specs/agents/codex.md` for Codex implementation work.
- `specs/agents/antigravity.md` for Antigravity planning and review work.
- `specs/architecture/nx-monorepo.md` for Nx workspace boundaries, targets, and dependency rules.
- `specs/architecture/fsd.md` for Feature-Sliced Design layering and migration rules.
- `apps/landing-page/specs/design.md` for the landing-page visual design system.
- `apps/landing-page/specs/codex.md` for landing-page-specific Codex behavior.
- `apps/landing-page/specs/antigravity.md` for landing-page-specific Antigravity behavior.

## Workspace Rules

- Treat this repository as an Nx monorepo.
- Follow the Nx monorepo architecture spec before moving code across projects.
- Apply Feature-Sliced Design gradually; do not split simple code before the app needs it.
- Keep app-specific work inside its app unless a shared workspace config must change.
- Do not use `any`, `unknown`, or explicit `undefined` in interfaces or object type shapes. Model real values with precise types, optional properties, discriminated unions, nullable fields, or generics instead.
- Prefer existing Nx targets for verification: `test`, `lint`, `build`, and `e2e`.
- Keep generated starter content out of final product surfaces.
- Do not introduce external services, live financial data, or compliance-sensitive claims unless a task explicitly asks for them.

## Current Projects

- `apps/landing-page`: Next.js App Router landing page.
- `apps/landing-page-e2e`: Playwright end-to-end tests for the landing page.

## Default Task Flow

1. Read the relevant app files and specs.
2. Identify the smallest complete change.
3. Implement within the correct app boundary.
4. Update unit or e2e tests when user-visible behavior changes.
5. Run focused verification.
6. Summarize changed files, verification, and any follow-up.
