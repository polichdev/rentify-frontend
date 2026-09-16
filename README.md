# Rentify Frontend — Rental Platform

[![React 19](https://img.shields.io/badge/React-19-61dafb.svg?logo=react&logoColor=black)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue.svg?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Vite](https://img.shields.io/badge/Vite-7.x-646CFF.svg?logo=vite&logoColor=white)](https://vite.dev/)
[![Tailwind CSS v4](https://img.shields.io/badge/Tailwind_CSS-v4-38b2ac.svg?logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![TanStack Query](https://img.shields.io/badge/TanStack_Query-v5-FF4154.svg?logo=reactquery&logoColor=white)](https://tanstack.com/query)
[![Leaflet](https://img.shields.io/badge/Leaflet-1.9-199900.svg?logo=leaflet&logoColor=white)](https://leafletjs.com/)
[![React Router](https://img.shields.io/badge/React_Router-v6-CA4245.svg?logo=reactrouter&logoColor=white)](https://reactrouter.com/)
[![Zustand](https://img.shields.io/badge/Zustand-v5-443E38.svg?logo=zustand&logoColor=white)](https://zustand.docs.pmnd.rs/)
[![React Hook Form](https://img.shields.io/badge/React_Hook_Form-v7-EC5990.svg?logo=reacthookform&logoColor=white)](https://react-hook-form.com/)
[![Zod](https://img.shields.io/badge/Zod-v4-3E67B1.svg?logo=zod&logoColor=white)](https://zod.dev/)
[![Axios](https://img.shields.io/badge/Axios-v1.x-5A29E4.svg?logo=axios&logoColor=white)](https://axios-http.com/)
[![i18next](https://img.shields.io/badge/i18next-v26-26A69A.svg?logo=i18next&logoColor=white)](https://www.i18next.com/)
[![Google OAuth](https://img.shields.io/badge/Google_OAuth-2.0-4285F4.svg?logo=google&logoColor=white)](https://developers.google.com/identity)
[![Vitest](https://img.shields.io/badge/Vitest-v4-6E9F18.svg?logo=vitest&logoColor=white)](https://vitest.dev/)

[![Docker](https://img.shields.io/badge/Docker-Multi--Stage-2496ED.svg?logo=docker&logoColor=white)](https://www.docker.com/)
[![Nginx](https://img.shields.io/badge/Nginx-Reverse_Proxy-009639.svg?logo=nginx&logoColor=white)](https://nginx.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## Overview

**Rentify** is a modern, high-performance real estate rental platform in Ukraine designed for discovering, booking, listing, and managing residential and commercial properties. It serves both guests seeking temporary housing or apartments and property owners looking to monetize listings with integrated booking management, real-time communication, promotional boost packages, and digital wallet transactions.

This repository contains the **React 19 + TypeScript Single Page Application (SPA)** that communicates with the Spring Boot backend (`rentify-backend`). The application is engineered with a modular, layer-separated architecture, responsive design with dark mode support, custom Leaflet map integration with Ukraine boundary masking, robust dual-strategy authentication (HTTP-only cookie / Bearer token), and zero-CLS layout states.

---

## Tech Stack

### Core Framework & Build Tooling
- **React 19.2**: Concurrent React features, modern hook composition, and optimized component lifecycles
- **TypeScript 5.9**: Strict type safety across all domain models, API payloads, form schemas, and event contracts
- **Vite 7.3**: Lightning-fast ESM dev server with hot module replacement (HMR) and optimized rollup production bundling

### State Management & Data Fetching
- **TanStack Query v5 (React Query)**: Declarative server-state management, automatic query caching, background revalidation, and mutation invalidations
- **Zustand v5**: Lightweight synchronous client-state management for modal controls, UI filters, and transient application states
- **React Context API**: Dedicated providers for authentication session state (`AuthContext`) and theme switching (`ThemeContext`)

### Maps, Geolocation & Visualization
- **Leaflet 1.9 & React Leaflet 5.0**: Interactive map engine with smooth panning, zooming, and dynamic viewport bounds
- **Custom Ukraine GeoJSON Mask Layer**: Regional polygon clipping masking neighboring territories to focus explicitly on Ukrainian oblasts and cities
- **Clustered Map Markers**: High-performance pin clustering with custom SVG icon markers and instant property popovers

### UI, Styling & Design System
- **Tailwind CSS v4**: High-performance modern utility styling with CSS variables for dynamic Dark / Light theme tokens
- **Lucide React**: Clean, consistent icon set across all navigation, cards, and modal components
- **React Hot Toast**: Unobtrusive notification system for feedback on async mutations, bookings, and auth updates

### Forms & Validation
- **React Hook Form v7**: Performant, uncontrolled form validation minimizing unnecessary re-renders
- **Zod v4 & @hookform/resolvers**: Schema-based runtime validation with strict TypeScript inference for auth, listing wizards, and profile editing

### HTTP Client & Network Layer
- **Axios 1.13**: Configured HTTP client featuring automatic deep snake_case-to-camelCase response transformation
- **CSRF Protection**: Automatic extraction and injection of `X-CSRF-Token` headers for mutating requests in cookie-based auth mode
- **Network & Session Interceptors**: Global event dispatching for session expiration (`auth:session-expired`) and offline detection (`network:offline`)

### Internationalization & SEO
- **i18next & react-i18next**: Pluggable internationalization architecture ready for multi-language localization
- **Dynamic SEO & Metadata Engine**: Custom OpenGraph tags, JSON-LD Schema.org structured breadcrumbs, and canonical link injection in `AppRouter`

### Testing & Code Quality
- **Vitest 4.1**: Next-generation test runner running 68+ unit and integration tests across 28 test suites
- **React Testing Library & Jest DOM**: User-centric component behavior and DOM mutation assertions
- **ESLint 9**: Strict linting rules with TypeScript and React Hooks validation plugins

### Production & Containerization
- **Multi-Stage Dockerfile**: Two-stage build (Node 22 Alpine builder into lightweight Nginx 1.27 Alpine static server)
- **Nginx Reverse Proxy**: Production SPA routing (`try_files $uri /index.html`) with `/api/` reverse proxy pass and enterprise security headers (CSP, HSTS, X-Frame-Options)

---

## Core Features

- **Interactive Map Search & Geo-Clustering**: Leaflet-powered map with custom Ukraine borders/mask, marker clustering, instant coordinate geocoding, synchronized viewport queries, and map card previews.
- **Advanced Search & Filtering Engine**: Instant keyword search, city selection, rental type (apartment, house, room, commercial), price range slider, room count, amenities filter, date availability, and multi-criteria sorting.
- **Comprehensive Property Management**: 6-step listing wizard (Basics, Location with interactive map picker, Property Details, Amenities, Pricing/Rules, Multi-image uploader with reordering & previews), draft saving, editing, and publishing.
- **Booking & Synthetic Payments Flow**: Tenant booking requests, date range picker with blocked dates validation, booking status workflows (Pending, Confirmed, Rejected, Cancelled), host booking inbox, and integrated wallet/synthetic payment processing.
- **Financial Wallet & Listing Promotions**: In-app digital wallet with balance display, transaction history ledger, synthetic top-up modal, and listing boost/promotion packages with active tier indicators.
- **Real-Time Host-Tenant In-App Chat**: Conversation threads tied to properties, instant messaging pane, unread message indicators, auto-retry on expired threads, and background polling synchronization.
- **User Profile & Public Host Showcase**: User account settings (avatar upload/deletion, profile info, password security update), host public profile page displaying all active listings, host rating, member since status, and contact options.
- **Dual Authentication & Security**: Email/password authentication, Google OAuth 2.0 integration, dual auth strategy (HTTP-Only Secure Cookie with CSRF protection or Bearer JWT), automatic session invalidation on 401, and route protection guards.
- **SEO & Layout Stability**: Dynamic OpenGraph tags, JSON-LD Schema.org structured breadcrumbs, canonical links, and zero-CLS skeleton screens tailored for auth, map, and content views.

---

## Screenshots

| Home Page | Search Listings |
| :---: | :---: |
| <img src="src/assets/images/home.jpg" alt="Home Page" width="100%" /><br/>*Landing page featuring hero search, popular Ukrainian cities & quick navigation.* | <img src="src/assets/images/search.jpg" alt="Search Listings" width="100%" /><br/>*Advanced search view with multi-parameter filter sidebar & sort controls.* |
| **Map Search** | **Property Details** |
| <img src="src/assets/images/map.jpg" alt="Map Search" width="100%" /><br/>*Interactive Leaflet map with Ukraine boundaries mask & marker clustering.* | <img src="src/assets/images/property_details.jpg" alt="Property Details" width="100%" /><br/>*Complete listing view with photo carousel, amenities & booking date calculator.* |
| **User Profile** | **Profile (Dark Mode)** |
| <img src="src/assets/images/profile.jpg" alt="User Profile" width="100%" /><br/>*User dashboard with tabs for properties, bookings, wallet & promotions.* | <img src="src/assets/images/profile_dark_mode.jpg" alt="Profile Dark Mode" width="100%" /><br/>*Dark theme support with high contrast across all dashboard interfaces.* |
| **Create a Listing Wizard** | |
| <img src="src/assets/images/create_property.jpg" alt="Create a Listing" width="100%" /><br/>*6-step guided wizard for creating and publishing properties with map coordinate selection.* | |

---

## System Architecture & Patterns

This application is built with a decoupled Single Page Application (SPA) architecture designed for reliability, speed, and strict separation of concerns.

### Frontend Architecture
- **Layer-Based & Modular Structure**: Divided cleanly into pages (`src/pages`), reusable component primitives (`src/components`), domain API services (`src/services`), custom React Query hooks (`src/hooks`), and global contexts (`src/contexts`).
- **Server State vs. Client State Isolation**: Server data is exclusively cached, synchronized, and invalidated through TanStack Query v5; client UI state (theme mode, modal visibility, filter controls) is encapsulated in Zustand stores and React Context.
- **Dual Authentication Strategy (Cookie & Bearer)**: Flexible support for both stateless Bearer JWT authentication and HTTP-Only cookies with CSRF validation (`X-CSRF-Token` header) for enhanced security.
- **Automated Deep Key Transformation**: Axios response interceptors automatically convert incoming `snake_case` JSON fields into idiomatic `camelCase` TypeScript objects, eliminating manual DTO mapping across the codebase.
- **Event-Driven Decoupling**: Global window events (`auth:session-expired`, `network:offline`) allow network interceptors to notify UI layers cleanly without creating circular dependencies.
- **Zero-CLS Skeleton Screens**: Purpose-built skeleton loaders tailored for auth forms, content grids, and fullscreen map views to guarantee zero Cumulative Layout Shift during data fetching.
- **Leaflet Ukraine Polygon Masking**: GeoJSON boundary layer highlighting Ukrainian territory while providing custom polygon clipping over surrounding areas for clear geographical context.

### Code Quality & Security
- **Strict Content Security Policy (CSP)**: Vite preview and Nginx configurations enforce strict CSP rules, frame-ancestors restrictions, and strict Referrer policies.
- **Automated Dependency Auditing**: Pre-configured `npm run audit:deps` and `npm run security:check` scripts verifying vulnerability-free production dependencies.
- **Automated Vitest Test Suite**: 28 test suites validating auth forms, chat message panes, map sync, profile cards, wizard steps, and search filters.

---

## Testing & Code Quality

The frontend is continuously tested using **Vitest** and **React Testing Library**:

- **28 Test Suites & 68+ Passing Tests**: Unit and integration tests covering auth workflows, property card rendering, chat components, create listing steps, and map viewport synchronization.
- **TypeScript Strict Mode**: Full compile-time type-safety with zero `any` leaks across domain DTOs.
- **ESLint 9 Rules**: Zero-warning enforcement across React hooks and modern ECMAScript standards.

To run tests locally:

```bash
# Run full Vitest test suite
npm test

# Run tests in watch mode
npm run test:watch

# Run linter and security checks
npm run lint
npm run security:check
```

---

## Repository Structure

```
rentify-frontend/
├── public/                       # Static public assets (icons, manifest)
├── src/
│   ├── api/                      # Centralized React Query keys (queryKeys.ts)
│   ├── assets/                   # Static images, SVG icons, and city photos
│   │   └── images/               # Screenshots and promotional imagery
│   ├── components/               # Reusable UI component modules
│   │   ├── auth/                 # Login, register, Google OAuth forms & modals
│   │   ├── chat/                 # Host-tenant messaging pane & thread list
│   │   ├── footer/               # Footer navigation & language selector
│   │   ├── home/                 # Hero section, city grid, FAQ accordion
│   │   ├── map/                  # Leaflet map, Ukraine mask, marker clusters
│   │   ├── navbar/               # Header navbar, search bar, user dropdown
│   │   ├── profile/              # Profile tabs (listings, bookings, wallet, promotions)
│   │   ├── property-card/        # Property card component with photo carousel
│   │   ├── property-create/      # 6-step listing creation wizard components
│   │   ├── property-details/     # Full listing view, gallery, amenities, review list
│   │   └── search/               # Search filter drawer, sort toolbar, results grid
│   ├── config/                   # API endpoints, route definitions, environment config
│   ├── constants/                # Static amenity definitions, property types, city list
│   ├── contexts/                 # Global React contexts (AuthContext, ThemeContext)
│   ├── hooks/                    # Custom React hooks (React Query wrappers, debouncers)
│   ├── layouts/                  # Layout wrappers (MainLayout, AuthLayout)
│   ├── pages/                    # Route page components (Home, Search, Details, Profile...)
│   ├── routes/                   # AppRouter with route guards, Suspense, SEO metadata
│   ├── services/                 # Axios API clients (auth, property, booking, wallet, chat)
│   ├── test/                     # Vitest test setup and mock handlers
│   ├── types/                    # TypeScript interfaces, DTOs, and domain models
│   ├── utils/                    # Data formatters, cookies, validation, geocoding helpers
│   ├── App.tsx                   # Main root component with Toast & Router providers
│   ├── index.css                 # Tailwind CSS v4 setup and theme variables
│   └── main.tsx                  # Application entry point with QueryClientProvider
├── Dockerfile                    # Multi-stage production container build (Node 22 -> Nginx)
├── nginx.conf                    # Nginx reverse proxy configuration for SPA & API
├── vite.config.ts                # Vite bundler configuration & dev server proxy
├── vitest.config.ts              # Vitest runner and environment configuration
├── tailwind.config.js            # Tailwind CSS configuration
├── tsconfig.json                 # Strict TypeScript compiler options
└── package.json                  # Dependencies, build scripts, and test commands
```

---

## Running Locally

### Prerequisites
- **Node.js**: `v20.x` or `v22.x` recommended
- **npm**: `v10.x` or higher
- **Backend API**: Running `rentify-backend` (Spring Boot) on `http://localhost:8080`

### Step 1: Clone Repository
```bash
git clone https://github.com/polchduikt/rentify-frontend.git
cd rentify-frontend
```

### Step 2: Install Dependencies
```bash
npm install
```

### Step 3: Configure Environment Variables
Create a `.env` (or `.env.local`) file in the project root:

```env
# API Base URL & Dev Proxy Target
VITE_API_BASE_URL=/api/v1
VITE_DEV_API_PROXY_TARGET=http://localhost:8080

# Authentication Strategy: 'cookie' (HTTP-only) or 'bearer' (JWT Header)
VITE_AUTH_STRATEGY=cookie

# CSRF Cookie and Header Configuration
VITE_CSRF_COOKIE_NAME=csrf_token
VITE_CSRF_HEADER_NAME=X-CSRF-Token

# Optional: Google OAuth 2.0 Client ID
VITE_GOOGLE_CLIENT_ID=your_google_oauth_client_id

# Optional: Canonical Site URL for SEO Metadata
VITE_SITE_URL=http://localhost:5173
```

### Step 4: Start Development Server
```bash
npm run dev
```

The application will be available at **[http://localhost:5173](http://localhost:5173)**.

---

## Run with Docker

This repository includes a production-ready multi-stage `Dockerfile` that builds the React application and serves it via Nginx on port 80.

### 1. Create Docker Network
```bash
docker network create rentify-net
```

### 2. Run Backend Container
```bash
docker run -d --name backend --network rentify-net -p 8080:8080 rentify-backend
```

### 3. Build Frontend Image
```bash
docker build -t rentify-frontend \
  --build-arg VITE_API_BASE_URL=/api/v1 \
  --build-arg VITE_AUTH_STRATEGY=cookie \
  --build-arg VITE_CSRF_COOKIE_NAME=csrf_token \
  --build-arg VITE_CSRF_HEADER_NAME=X-CSRF-Token \
  --build-arg VITE_GOOGLE_CLIENT_ID=your_google_oauth_client_id \
  .
```

### 4. Run Frontend Container
```bash
docker run -d --name rentify-frontend --network rentify-net -p 5173:80 rentify-frontend
```

Access the containerized application at **[http://localhost:5173](http://localhost:5173)**.

---

## Available Local Endpoints

| Service | Address | Description |
| :--- | :--- | :--- |
| **Frontend Web App** | `http://localhost:5173` | React 19 Single Page Application |
| **Backend REST API** | `http://localhost:8080/api/v1` | Spring Boot REST API Service |
| **Swagger / OpenAPI** | `http://localhost:8080/swagger-ui.html` | Backend Interactive API Documentation |

---

## API Integration & Documentation

The frontend communicates with the `rentify-backend` REST API (Spring Boot). Full endpoint specifications, payload schemas, authentication workflows, CSRF protection, and error handling lifecycle events are documented in:

**[API Integration Guide (docs/API_INTEGRATION.md)](docs/API_INTEGRATION.md)**

---

## Status

Rentify Frontend is actively maintained, continuously tested, and updated with modern React standards and performance optimizations.

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

