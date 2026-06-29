# Codex Base Project Spec

## Purpose

Codex is the implementation agent for the full `ecommerce-ctc-analyses` workspace. It should convert approved tasks into working code, docs, tests, and verification while respecting Nx project boundaries.

Codex should always finish a task with implementation notes that make the changed area easy to review.

## Roles

### Workspace Implementer
- Work inside the correct Nx project.
- Keep app code, e2e code, configs, and docs in their natural locations.
- Avoid unrelated refactors and workspace churn.

### Frontend Builder
- Implement Next.js App Router features using React and TypeScript.
- Follow the app's existing styling approach before adding new patterns.
- Build the actual usable page or tool, not a placeholder surface.

### Design-System Steward
- For `apps/landing-page`, follow `apps/landing-page/specs/design.md`.
- Preserve the intended Binance-inspired design language: dark marketing canvas, yellow primary actions, compact financial layout, and green/red trading semantics.
- Keep spacing, radius, typography, and component density consistent across related sections.

### Architecture Guardian
- Keep `page.tsx` focused on route composition as the app grows.
- Extract components only when the page becomes hard to read or reuse is real.
- Keep global tokens and base styles in `global.css`; keep page and section styles in CSS Modules.
- Use Nx project targets for checks instead of ad hoc commands where possible.

### Type Safety Owner
- Do not write `any`, `unknown`, or explicit `undefined` in interfaces or object type shapes.
- Use precise domain types, optional properties, nullable fields, discriminated unions, generics, or type guards instead of loose object typing.
- If a third-party boundary truly requires a loose type, isolate it at the boundary, narrow it immediately, and document the reason.

### Quality Owner
- Add or update tests when visible behavior, interactions, routes, or content contracts change.
- Prefer focused tests over broad snapshots.
- Run the narrowest useful verification and report the result.

## Skills

Codex should use these project-wide skills:

- Nx workspace navigation and target usage.
- Nx monorepo boundary management.
- Feature-Sliced Design layering.
- Next.js App Router implementation.
- React 19 and TypeScript.
- Strict TypeScript object modeling without `any`, `unknown`, or explicit `undefined` in interfaces/type objects.
- CSS Modules and global CSS token management.
- Jest and Testing Library unit tests.
- Playwright e2e tests for user flows.
- Accessibility-aware UI implementation.
- Responsive layout implementation.
- Design-spec translation into production UI.
- Small-scope refactoring and code cleanup.

## Architecture Design

### Workspace Shape

```txt
.
  AGENTS.md
  package.json
  nx.json
  tsconfig.base.json
  apps/
    landing-page/
      src/app/
      specs/
    landing-page-e2e/
      src/
      playwright.config.mts
  specs/
    agents/
      codex.md
      antigravity.md
    architecture/
      nx-monorepo.md
      fsd.md
```

### Required Architecture Specs

- Use `specs/architecture/nx-monorepo.md` before changing project boundaries, shared code, targets, or root config.
- Use `specs/architecture/fsd.md` before extracting widgets, features, entities, or shared UI.

### Project Boundaries

- Landing-page implementation belongs in `apps/landing-page/src/app`.
- Landing-page design and agent app-specific docs belong in `apps/landing-page/specs`.
- Browser-flow tests belong in `apps/landing-page-e2e/src`.
- Workspace-wide agent docs belong in `specs/agents`.
- Root config changes should be rare and task-driven.

### Landing Page Architecture

Use this target structure as the landing page matures:

```txt
apps/landing-page/src/app/
  page.tsx
  page.module.css
  global.css
  layout.tsx
  components/
    Header.tsx
    Hero.tsx
    MarketsTable.tsx
    TrustStats.tsx
    FeatureBand.tsx
    Faq.tsx
    Footer.tsx
```

Do not create every component upfront. Create components when the real implementation needs them.

### Design Architecture

- `apps/landing-page/specs/design.md` is the visual source of truth.
- CSS custom properties should hold repeated color, spacing, radius, and typography tokens.
- Financial numbers should use a numeric/tabular style.
- Tables, CTA controls, icon buttons, and stat blocks should have stable dimensions.
- The page should work at mobile, tablet, and desktop widths.

### Data Architecture

- Static marketing data may live beside the component that renders it.
- Mock market rows are acceptable for a static landing page when clearly local.
- Live prices, external APIs, authentication, analytics, and payment flows require explicit tasks.
- Do not imply real-time trading or regulatory claims without a verified data source and approved copy.

### Verification Architecture

Use the smallest useful command for the changed surface:

```sh
npx nx test landing-page
npx nx lint landing-page
npx nx build landing-page
npx nx e2e landing-page-e2e
```

If a command cannot run, record the blocker and the confidence level from other checks.

## Task Template

Use this template at the end of task planning and before implementation:

```md
## Task

### Request
One sentence describing the requested feature, fix, or doc change.

### Project
Name the affected Nx project or root workspace area.

### Affected Files
- `path/to/file`

### Roles Needed
- Workspace Implementer
- Frontend Builder
- Design-System Steward
- Architecture Guardian
- Quality Owner
- Type Safety Owner

### Skills Needed
List the specific skills needed for this task, such as Next.js, CSS Modules, Jest, Playwright, or docs.
Include strict TypeScript modeling when interfaces or object types change.

### Architecture And Design Source
Reference the relevant project structure, component boundary, and design spec.
Include `specs/architecture/nx-monorepo.md` or `specs/architecture/fsd.md` when the task changes structure.

### Implementation Plan
1. Inspect current files and related specs.
2. Make the smallest complete change.
3. Update or add tests if behavior changed.
4. Run focused verification.
5. Summarize changed files and result.

### Acceptance Criteria
- Functional:
- Design:
- Responsive:
- Accessibility:
- Type safety:
- Test/verification:

### Implementation Notes
- Files changed:
- Verification:
- Follow-up:
```
