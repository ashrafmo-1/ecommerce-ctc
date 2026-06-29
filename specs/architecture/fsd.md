# Feature-Sliced Design Architecture Spec

## Purpose

Feature-Sliced Design, or FSD, gives frontend code a predictable structure as the landing page grows. The workspace should use FSD ideas gradually: start simple, then introduce layers and slices when the app has enough real product surface to benefit.

## Layer Order

Use this dependency direction from highest to lowest:

```txt
app
pages
widgets
features
entities
shared
```

Higher layers may import lower layers. Lower layers must not import higher layers.

## Layer Meanings

### app

Application setup and route integration.

In Next.js App Router, `src/app` is required by the framework. Keep route files there.

Examples:

```txt
apps/landing-page/src/app/layout.tsx
apps/landing-page/src/app/page.tsx
apps/landing-page/src/app/global.css
```

### pages

Page-level composition for a route when it becomes too large for `src/app/page.tsx`.

For the current app, this layer is optional. Add it only when the landing page needs a clear page model outside the Next route file.

Example:

```txt
apps/landing-page/src/pages/home/ui/HomePage.tsx
```

### widgets

Large page sections assembled from smaller pieces.

Landing-page examples:

```txt
apps/landing-page/src/widgets/header/ui/Header.tsx
apps/landing-page/src/widgets/hero/ui/Hero.tsx
apps/landing-page/src/widgets/markets-table/ui/MarketsTable.tsx
apps/landing-page/src/widgets/faq/ui/Faq.tsx
apps/landing-page/src/widgets/footer/ui/Footer.tsx
```

### features

User actions or business capabilities.

Landing-page examples:

```txt
apps/landing-page/src/features/sign-up-cta/ui/SignUpCta.tsx
apps/landing-page/src/features/market-search/ui/MarketSearch.tsx
apps/landing-page/src/features/faq-toggle/ui/FaqToggle.tsx
```

### entities

Business objects and their local model, formatting, or display primitives.

Landing-page examples:

```txt
apps/landing-page/src/entities/market/model/marketRows.ts
apps/landing-page/src/entities/market/ui/MarketPair.tsx
apps/landing-page/src/entities/faq/model/faqItems.ts
```

### shared

Reusable, domain-neutral code.

Examples:

```txt
apps/landing-page/src/shared/ui/Button.tsx
apps/landing-page/src/shared/ui/IconButton.tsx
apps/landing-page/src/shared/lib/formatPercent.ts
apps/landing-page/src/shared/config/navItems.ts
```

Keep `shared` small. If code knows about Binance, markets, FAQ, signup, or landing-page content, it probably belongs in `widgets`, `features`, or `entities`.

## Public API Rule

Each slice should expose imports through an `index.ts` file once it has more than one internal file.

Good:

```ts
import { MarketsTable } from '@/widgets/markets-table';
```

Avoid:

```ts
import { MarketsTable } from '@/widgets/markets-table/ui/MarketsTable';
```

Do not add barrel files before they help. A one-file slice can be imported directly until it grows.

## Slice Structure

Use this structure for larger slices:

```txt
slice-name/
  index.ts
  ui/
  model/
  lib/
  api/
```

- `ui` contains React components and CSS Modules.
- `model` contains local data, types, constants, and state.
- `lib` contains slice-specific helpers.
- `api` contains data-fetching code when an explicit task introduces it.

## Landing-Page Migration Path

Start with the current simple structure:

```txt
apps/landing-page/src/app/page.tsx
apps/landing-page/src/app/page.module.css
apps/landing-page/src/app/global.css
```

When the page becomes real, first extract widgets:

```txt
apps/landing-page/src/widgets/hero/
apps/landing-page/src/widgets/markets-table/
apps/landing-page/src/widgets/trust-stats/
apps/landing-page/src/widgets/feature-band/
apps/landing-page/src/widgets/faq/
apps/landing-page/src/widgets/footer/
```

Then extract entities when repeated data or formatting appears:

```txt
apps/landing-page/src/entities/market/
apps/landing-page/src/entities/faq/
```

Add features only when there is user interaction:

```txt
apps/landing-page/src/features/market-search/
apps/landing-page/src/features/faq-toggle/
```

Add shared UI only after at least two widgets need the same component.

## Import Rules

- `app` can import from all lower layers.
- `widgets` can import from `features`, `entities`, and `shared`.
- `features` can import from `entities` and `shared`.
- `entities` can import from `shared`.
- `shared` imports from nothing app-specific.
- Same-layer imports between slices should be avoided unless the dependency is deliberately promoted to a lower layer.

## Styling Rules

- Slice-specific styles live beside the slice UI.
- Global design tokens remain in `src/app/global.css`.
- Shared UI components may have their own CSS Modules.
- Do not duplicate design tokens inside slices.

## Testing Rules

- Test user-visible behavior at the highest useful layer.
- Entity formatting and model helpers can have focused unit tests.
- Widgets can be covered by page-level render tests unless they contain meaningful branching.
- Feature interactions should have direct interaction tests or e2e coverage.

## When Not To Use FSD

Do not split code into FSD layers when:

- The page is still a simple one-file implementation.
- A component is used only once and is easy to read in place.
- The split would create more navigation than clarity.
- There is no stable domain language yet.

## Task Template

Use this checklist for any FSD architecture task:

```md
## FSD Architecture Task

### Slice
What slice is being created or changed?

### Layer
Which layer owns it: app, pages, widgets, features, entities, or shared?

### Public API
Does the slice need an `index.ts` yet?

### Dependencies
Which lower layers does it import?

### Styling
Where do the CSS module and global tokens live?

### Tests
What is the highest useful layer to test?

### Acceptance Criteria
- Layer direction is respected.
- Slice name matches domain language.
- Shared code is domain-neutral.
- Imports use the public API when the slice has one.
- The split makes the code easier to understand.
```
