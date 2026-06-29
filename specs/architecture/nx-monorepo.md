# Nx Monorepo Architecture Spec

## Purpose

This workspace uses Nx to organize apps, tests, and future shared libraries. The goal is to keep each project isolated, make verification predictable, and allow shared code only when reuse becomes real.

## Current Workspace Shape

```txt
.
  apps/
    landing-page/
      src/app/
      specs/
    landing-page-e2e/
      src/
      playwright.config.mts
  specs/
    agents/
    architecture/
  nx.json
  package.json
  tsconfig.base.json
```

## Project Types

### Applications

Applications live in `apps/`.

- `apps/landing-page` is the Next.js App Router application.
- App code should own routes, layouts, page-specific components, styles, and app-local data.
- App-local specs belong inside the app's `specs/` folder.

### End-To-End Projects

End-to-end tests live as separate Nx projects.

- `apps/landing-page-e2e` owns Playwright tests for `apps/landing-page`.
- E2E tests should verify complete user journeys, not component internals.
- Unit tests should stay in the app project unless a browser flow is required.

### Shared Libraries

Shared libraries should be introduced only when there is real reuse.

Preferred future locations:

```txt
libs/
  shared/
    ui/
    config/
    testing/
  landing-page/
    features/
    entities/
```

Do not create `libs/` just to move code away from an app. Keep code local until a second consumer or clear boundary exists.

## Ownership Rules

- Route and page composition belongs to the owning app.
- App-specific visual design belongs to the app.
- Workspace-wide agent and architecture specs belong in `specs/`.
- Root config changes must be justified by a workspace-wide need.
- E2E setup belongs to the e2e project, not the application project.

## Dependency Rules

- Apps may depend on shared libraries.
- Apps should not depend on other apps.
- E2E projects may depend on the app they test through browser behavior, not direct imports from app internals.
- Shared libraries should not depend on app projects.
- Low-level shared libraries should not depend on higher-level feature libraries.

## Target Rules

Use Nx targets for repeatable work:

```sh
npx nx dev landing-page
npx nx test landing-page
npx nx lint landing-page
npx nx build landing-page
npx nx e2e landing-page-e2e
```

Use the smallest target that proves the change. For example, documentation-only changes do not need app tests.

## Naming Rules

- Project names should be clear and domain-based: `landing-page`, `landing-page-e2e`.
- Library names should describe ownership and layer: `shared-ui`, `landing-page-features`.
- Files should use descriptive names that match their exported purpose.
- Avoid generic names like `utils` unless the code is genuinely cross-cutting and narrow.

## Configuration Rules

- Keep TypeScript configuration centralized unless a project needs a specific override.
- Keep lint and test configuration project-local when behavior differs by project.
- Do not add new workspace plugins without a task that needs them.
- Do not add global path aliases until there is a shared library or repeated import pain.

## Testing Strategy

- Use Jest and Testing Library for component rendering, visible copy, and local interactions.
- Use Playwright for navigation, browser layout confidence, and full user journeys.
- Add tests near the project that owns the behavior.
- Avoid broad snapshots for large pages.

## When To Add A Library

Add a library only when at least one is true:

- Two or more projects need the same code.
- A domain area is large enough that a project boundary improves ownership.
- A testing helper or config is reused across projects.
- A UI kit has stable reusable components.

Do not add a library for a single section, one helper, or speculative future reuse.

## Task Template

Use this checklist for any Nx architecture task:

```md
## Nx Architecture Task

### Project
Which Nx project or workspace area changes?

### Boundary
What owns this code: app, e2e project, shared library, or root config?

### Dependency Direction
Does the change preserve app-to-lib and e2e-through-browser boundaries?

### Target
Which Nx target verifies the change?

### Acceptance Criteria
- Code is in the correct project.
- No app imports another app.
- Shared code exists only when reuse is real.
- The smallest useful Nx verification passes or the blocker is documented.
```
