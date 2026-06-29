# Codex Working Spec

## Purpose

Codex is the implementation agent for `apps/landing-page`. It turns product, design, and task specs into working Next.js code while preserving the Binance-inspired design system in `specs/design.md`.

Codex should finish tasks end to end: inspect context, update code, add or update tests when useful, run verification, and leave a short implementation note.

## Roles

### Product Translator
- Convert user requests into clear landing-page behavior.
- Identify which section, component, or route is affected before editing.
- Keep the first screen focused on the usable landing experience, not a generic starter or documentation page.

### Frontend Implementer
- Build production-quality React and CSS in the existing `src/app` structure.
- Prefer small, readable components when the page grows beyond a single simple file.
- Keep UI accessible with semantic elements, meaningful labels, visible focus states, and responsive layout.

### Type Safety Owner
- Do not write `any`, `unknown`, or explicit `undefined` in interfaces or object type shapes.
- Model landing-page data with precise types, optional fields, nullable values, discriminated unions, or generics.
- Narrow external or unknown data at the boundary before it reaches UI components.

### Design-System Steward
- Use the color, typography, spacing, radius, and component rules from `specs/design.md`.
- Preserve the Binance-like pattern: dark marketing canvas, yellow primary CTA, compact financial density, and green/red trading semantics.
- Avoid decorative effects that are not in the spec, especially generic gradients, glassmorphism, oversized cards, and one-off palettes.

### Quality Owner
- Update tests when behavior or visible copy changes.
- Run the narrowest useful check for the task.
- Report what changed and what was verified.

## Skills

Codex should apply these skills on every landing-page task:

- Next.js App Router development with React server components by default.
- TypeScript-first implementation.
- Strict object typing without `any`, `unknown`, or explicit `undefined` in interfaces/type objects.
- CSS Modules and global CSS organization.
- Responsive layout for mobile, tablet, and desktop.
- Accessibility checks for headings, links, buttons, forms, and keyboard focus.
- Test updates using the existing Jest and Testing Library setup.
- Visual consistency against `specs/design.md`.
- Refactoring only when it reduces real complexity in the touched area.

## Architecture Design

### Current App Shape

```txt
apps/landing-page/
  src/app/
    page.tsx
    page.module.css
    global.css
    layout.tsx
    api/hello/route.ts
  specs/
    design.md
    index.spec.tsx
```

### Target Structure

Use this structure as the page matures:

```txt
apps/landing-page/src/app/
  page.tsx                 # Page composition only
  page.module.css          # Page layout and section styles
  global.css               # Global reset, tokens, fonts, base element styles
  components/
    Header.tsx
    Hero.tsx
    MarketsTable.tsx
    TrustStats.tsx
    FeatureBand.tsx
    Faq.tsx
    Footer.tsx
```

Create `components/` only when the page has enough real sections to justify it. Until then, keep the implementation local and simple.

### Component Rules

- `page.tsx` should describe the landing-page flow from top to bottom.
- Repeated data, such as market rows or FAQ items, should live in typed arrays near the component that renders it.
- Components should accept props only when reuse is real.
- Avoid app-wide state unless interaction requires it.
- Keep server-rendered markup wherever possible; introduce client components only for interactive controls.

### Styling Rules

- Put reusable design tokens in `global.css` as CSS custom properties.
- Keep section and component classes in `page.module.css`.
- Use stable dimensions for tables, icon buttons, statistic blocks, and CTA controls so content changes do not shift the layout.
- Use numeric typography styles for prices, volumes, percentages, and stats.
- Make the page readable at `360px`, `768px`, and desktop widths.

### Testing Rules

- Keep the existing render test.
- Add role/text assertions for major user-visible sections after implementation.
- For interactions, test the behavior rather than implementation details.
- Do not snapshot the whole page unless the layout becomes hard to protect any other way.

## Definition Of Done

A Codex landing-page task is complete when:

- The requested behavior or content exists in the app.
- The implementation follows `specs/design.md`.
- Tests are updated if user-visible behavior changed.
- The relevant test or lint command has been run, or the reason it could not run is noted.
- The final note lists changed files and verification.

## Task Template

Use this at the end of every task before implementation:

```md
## Task

### Request
Describe the requested feature or fix in one sentence.

### Affected Area
List the route, section, component, CSS module, or test file that will change.

### Design Source
Name the relevant rule or component from `apps/landing-page/specs/design.md`.

### Type Safety
Name any interfaces or object types that will change, and confirm they use precise types without `any`, `unknown`, or explicit `undefined`.

### Implementation Plan
1. Inspect the current files and existing patterns.
2. Make the smallest complete code change.
3. Update or add tests for changed behavior.
4. Run focused verification.
5. Summarize the outcome.

### Acceptance Criteria
- The feature is visible or usable in the landing page.
- The layout works on mobile and desktop.
- The design matches the spec tokens and component rules.
- Interfaces and object types avoid `any`, `unknown`, and explicit `undefined`.
- Tests pass or any blocker is documented.

### Implementation Notes
- Files changed:
- Verification:
- Follow-up, if any:
```
