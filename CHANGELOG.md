# Changelog

All notable changes to **Rentify Frontend** will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Comprehensive architectural documentation suite in `docs/` (`ARCHITECTURE.md`, `STRUCTURE.md`, `STATE_MANAGEMENT.md`, `FAILURES_AND_RECOVERY.md`, `THREAT_MODEL.md`, `MAPS_AND_GEO.md`).
- Architecture Decision Records (`ADR-001` through `ADR-005`) capturing foundational technical selections.
- Open-source governance files (`CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`).

## [1.0.0] - 2026-09-16

### Added
- **Core Architecture**:
  - React 19 Single Page Application scaffolded with Vite 7 and TypeScript 5.9.
  - Tailwind CSS v4 styling with dynamic Light / Dark mode token support.
  - TanStack Query v5 declarative caching and query state orchestration.
  - Zustand v5 client UI state store.
- **Geographic Exploration & Mapping**:
  - Leaflet 1.9 engine with smooth viewport bounds animation.
  - Ukraine sovereign border inverted GeoJSON polygon mask.
  - Dynamic marker clustering with interactive price tags.
- **Security & Authentication**:
  - Dual-strategy session management (HttpOnly cookie with CSRF double-submit / Bearer token storage).
  - Google OAuth 2.0 integration via Google Identity Services.
  - Global `401 Unauthorized` interceptor with `auth:session-expired` broadcast.
- **Features**:
  - Real estate search engine with multi-criteria filters (price, rooms, city, amenities).
  - Property creation and edit wizard with multi-step validation via React Hook Form and Zod.
  - Booking management system with date-range reservation checks.
  - Real-time conversation and chat floating panel.
  - User wallet dashboard and financial transaction logs.
- **Testing & Tooling**:
  - Vitest test suite with React Testing Library and JSDOM integration.
  - Multi-stage production `Dockerfile` with optimized Alpine Nginx reverse proxy.
