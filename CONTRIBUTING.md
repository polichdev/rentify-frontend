# Contributing to Rentify Frontend

Thank you for your interest in contributing to **Rentify**! This document provides guidelines for code style, branching strategy, testing expectations, and the pull request lifecycle.

---

## 1. Development Prerequisites

- **Node.js**: `20.x` or `22.x` (LTS recommended)
- **Package Manager**: `npm` (v10+)
- **Git**: Configured with your name and email

---

## 2. Local Setup & Workflow

1. **Clone the repository**:
   ```bash
   git clone https://github.com/polichdev/rentify-frontend.git
   cd rentify-frontend
   ```

2. **Install dependencies**:
   ```bash
   npm install
   ```

3. **Configure Environment Variables**:
   Copy `.env` to `.env.local` if you need custom backend endpoints:
   ```env
   VITE_API_BASE_URL=http://localhost:8080/api/v1
   VITE_USE_HTTP_ONLY_AUTH_COOKIE=false
   ```

4. **Start the local dev server**:
   ```bash
   npm run dev
   ```

---

## 3. Branching Strategy & Git Conventions

We follow a structured Git branching model:
- `main`: Production-ready code.
- `develop`: Primary integration branch.
- Feature branches: `feature/<ticket-or-short-description>` (e.g., `feature/booking-calendar-sync`).
- Fix branches: `fix/<issue-description>` (e.g., `fix/leaflet-mask-opacity`).

### Commit Message Guidelines (Conventional Commits)
All commit messages must adhere to the [Conventional Commits](https://www.conventionalcommits.org/) specification:
- `feat(map): add cluster zoom animations`
- `fix(auth): prevent double trigger on 401 session expiry`
- `refactor(services): normalize user DTO via adapter`
- `test(property): add unit tests for price calculation utility`
- `docs(adr): record decision on tanstack query cache TTL`

---

## 4. Quality & Pre-PR Verification

Before opening a Pull Request, ensure all quality gates pass:

```bash
# 1. Run unit and integration tests
npm run test

# 2. Run ESLint checks
npm run lint

# 3. Run security dependency audit and static verification
npm run security:check

# 4. Verify production build bundling
npm run build
```

---

## 5. Architectural Standards

- **Zero Direct Axios Calls in Components**: All API communication must flow through `src/services/` and `src/hooks/`.
- **Strict TypeScript**: Avoid `any` or `@ts-ignore`. If a type is unknown, use `unknown` and narrow it using type guards or Zod schemas.
- **Pure Utility Functions**: Functions in `src/utils/` must be pure and covered by unit tests in `*.test.ts`.
- **Architectural Changes**: Any foundational changes (new third-party state libraries, routing paradigm shifts) require an accompanying ADR in `docs/adr/`.
