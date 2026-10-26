# ADR-005: Tooling Pipeline: Vite 7 and Tailwind CSS v4 Engine

## Status
Accepted

## Context
Frontends require rapid local development feedback (sub-second HMR) and minimal production bundle sizes. Traditional bundlers (Webpack, Create React App) exhibit slow startup times and large compilation overheads. Additionally, utility-first CSS frameworks traditionally required separate PostCSS configurations and config file scaffolding.

## Decision
We standardize on:
1. **Vite 7**: Native ESM development server with lightning-fast Hot Module Replacement and Rollup production builds.
2. **Tailwind CSS v4** via `@tailwindcss/vite`: Utilizes the modernized Rust-based LightningCSS engine without requiring `tailwind.config.js` or complex PostCSS pipelines.
3. **Vitest 4**: Unit test runner sharing the same Vite configuration, aliases, and transformation pipeline.

## Consequences

### Positive
- Sub-50ms cold starts in local development.
- Single configuration (`vite.config.ts`) shared across development, testing, and production builds.
- CSS variables directly mapped to Dark / Light theme tokens in `src/index.css`.

### Negative
- Tailwind CSS v4 syntax changes from v3 require adapting to newer directive formats (`@theme`, `@import "tailwindcss"`).
