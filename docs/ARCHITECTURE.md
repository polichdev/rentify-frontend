# Rentify Frontend Architecture & System Design

## 1. Overview

**Rentify Frontend** is a Single-Page Application (SPA) designed for long- and short-term property rental in Ukraine. It interfaces with the Spring Boot backend (`rentify-backend`) to deliver real-time property searching, interactive geo-spatial exploration, booking management, user messaging, profile administration, promotional boosts, and digital wallet transactions.

The architecture emphasizes:
- **Strict Layer Separation**: UI presentation is decoupled from server caching, business logic, and transport clients.
- **Dual-Strategy Session Security**: Supports both secure HttpOnly cookie sessions with CSRF double-submit protection and Bearer token headers for headless/mobile compatibility.
- **Geographic Precision**: Dedicated Ukraine polygon mask layer and bounding boxes to ensure search operations remain bounded to Ukrainian territory.
- **Deterministic State Isolation**: Clear partition between server state (TanStack Query), synchronous UI state (Zustand), and reactive session contexts.

---

## 2. High-Level System Architecture

The following diagram illustrates how the client-side single page application interacts with external services, backend micro-modules, and rendering layers:

```mermaid
flowchart TB
    subgraph ClientBrowser["Client Browser (React 19 + TypeScript)"]
        direction TB
        subgraph ViewLayer["Presentation & Interaction Layer"]
            PAGES["Pages (SearchPage, PropertyDetails, Profile, Booking)"]
            COMPONENTS["Feature Components & Shared UI (Tailwind v4)"]
            LEAFLET_UI["Leaflet Map Engine & Ukraine Mask Layer"]
            CHAT_WIDGET["Floating Chat & Conversation Pane"]
        end

        subgraph StateLayer["State Management & Coordination"]
            REACT_QUERY["TanStack Query v5 (Server State & Cache)"]
            ZUSTAND_STORE["Zustand Stores (Search Filters, Chat State)"]
            AUTH_CONTEXT["AuthContext (Session Lifecycle & User Role)"]
            THEME_CONTEXT["ThemeContext (Dark / Light Theme Tokens)"]
        end

        subgraph ServiceLayer["Service & Adapter Layer"]
            SERVICES["Services (propertyService, bookingService, etc.)"]
            ADAPTERS["DTO Adapters (camelCase normalization, schema mapping)"]
            AXIOS_CLIENT["Axios Client (Interceptors, CSRF & 401 Dispatch)"]
        end
    end

    subgraph ExternalBackends["Backend & External Services"]
        SPRING_BACKEND["Rentify Spring Boot Backend (:8080)"]
        SPRING_AUTH["Spring Security (OAuth2 / JWT / Cookie Sessions)"]
        OSM_TILES["OpenStreetMap Tile Servers / CartoDB"]
        GEO_NOMINATIM["OpenStreetMap Nominatim Geocoding API"]
        GOOGLE_OAUTH["Google Identity Services (OAuth 2.0 SDK)"]
    end

    %% Wiring
    PAGES --> COMPONENTS
    COMPONENTS --> ZUSTAND_STORE
    COMPONENTS --> REACT_QUERY
    COMPONENTS --> AUTH_CONTEXT

    REACT_QUERY --> SERVICES
    SERVICES --> ADAPTERS
    ADAPTERS --> AXIOS_CLIENT

    AXIOS_CLIENT -->|REST + CSRF / Bearer| SPRING_BACKEND
    AUTH_CONTEXT -->|OAuth2 Credential Exchange| GOOGLE_OAUTH
    AUTH_CONTEXT -->|Session Probe / Me| SPRING_AUTH
    LEAFLET_UI -->|Raster Tiles HTTP/S| OSM_TILES
    LEAFLET_UI -->|Reverse Geocoding / Search| GEO_NOMINATIM
```

---

## 3. Core Architectural Layers

### 3.1 Presentation Layer (`src/pages`, `src/layouts`, `src/components`)
- **Pages**: Top-level route containers registered in `src/routes/AppRouter.tsx`. Pages coordinate data subscriptions and layout composition but delegate direct rendering to feature components.
- **Layouts**:
  - `MainLayout`: Persists navigation header, breadcrumb containers, floating chat trigger, and global footer.
  - `AuthLayout`: Dedicated shell for authentication, password recovery, and onboarding journeys.
- **Components**: Grouped by domain (`auth`, `chat`, `home`, `map`, `profile`, `property`, `reviews`, `search`, `ui`). Components are strictly typed and communicate via props and Zustand actions.

### 3.2 State Management Partitioning

To avoid global store pollution and synchronization bugs, state is partitioned into three distinct tiers:

```mermaid
graph LR
    subgraph ServerState["Server-Owned State"]
        RQ["TanStack Query Cache"]
        RQ_DESC["- Properties list & pagination<br/>- Booking requests & statuses<br/>- User profiles & wallet balance<br/>- Review threads"]
    end

    subgraph ClientTransient["Client Transient UI State"]
        ZS["Zustand Stores"]
        ZS_DESC["- Filter drawer open/close<br/>- Multi-step form drafts<br/>- Chat panel active thread<br/>- Local map zoom & pin focus"]
    end

    subgraph AmbientSession["Ambient Session State"]
        CTX["React Context API"]
        CTX_DESC["- Auth user credentials<br/>- Selected theme (Dark / Light)<br/>- Global event broadcast bus"]
    end
```

### 3.3 Service & Transport Layer (`src/services`, `src/services/adapters`)
- **Axios Singleton (`src/services/api.ts`)**:
  - Automatic `snake_case` to `camelCase` deep transformation on all incoming JSON payloads.
  - Automatic injection of `X-Requested-With: XMLHttpRequest`.
  - Automatic CSRF extraction from cookies for unsafe HTTP mutations (`POST`, `PUT`, `PATCH`, `DELETE`) when running in cookie mode.
  - Dispatches decoupled window events (`auth:session-expired`, `network:offline`) on transport failures.
- **Adapters**: Normalizes polymorphic backend responses into clean domain types (`src/types/*.ts`), isolating the UI from backend schema deprecations.

---

## 4. Key Runtime Data Flows

### 4.1 Authentication & Session Verification Flow

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant App as React SPA (main.tsx)
    participant AuthCtx as AuthContext
    participant Api as Axios Client (api.ts)
    participant Backend as Spring Boot Auth Controller

    User->>App: Opens Application
    App->>AuthCtx: Mount Provider
    AuthCtx->>Api: GET /api/v1/users/me (Session Probe)
    alt Session Valid (Cookie or Token)
        Api->>Backend: HTTP Request (credentials: include)
        Backend-->>Api: 200 OK + UserPayload JSON
        Api-->>AuthCtx: Normalized User Model
        AuthCtx-->>App: Set state (isAuthenticated = true)
    else Session Expired / Unauthorized
        Api->>Backend: HTTP Request
        Backend-->>Api: 401 Unauthorized
        Api-->>AuthCtx: Swallow initial probe error
        AuthCtx-->>App: Set state (isAuthenticated = false, user = null)
    end
```

### 4.2 Property Search & Geo-Filter Synchronization

```mermaid
sequenceDiagram
    autonumber
    actor Guest
    participant SearchPage as SearchPage / SearchMapPage
    participant FilterHook as useSearchPage()
    participant URLState as React Router (useSearchParams)
    participant QueryEngine as TanStack Query (queryKeys.properties)
    participant Backend as /api/v1/properties/search

    Guest->>SearchPage: Selects City & Price Range
    SearchPage->>FilterHook: updateFilters({ city: 'Lviv', maxPrice: 15000 })
    FilterHook->>URLState: Sync state to Browser URL (?city=Lviv&maxPrice=15000)
    URLState-->>FilterHook: URL params change detected
    FilterHook->>QueryEngine: Invalidate / Trigger query with new params
    QueryEngine->>Backend: GET /api/v1/properties/search?city=Lviv&maxPrice=15000
    Backend-->>QueryEngine: Paginated Property List + Geo Coordinates
    QueryEngine-->>SearchPage: Deliver cache payload
    SearchPage-->>Guest: Update Map Pins & Property Cards (Zero layout shift)
```

---

## 5. Map & Geolocation Subsystem

Rentify uses a custom geo-rendering pipeline on top of Leaflet 1.9:
1. **Coordinate Reference System**: WGS 84 (`EPSG:3857`).
2. **Boundary Protection (`src/utils/ukraineMask.ts`)**:
   - Inverted polygon GeoJSON mask covering territories outside Ukraine's borders with an adjustable opacity overlay.
   - Restricts map bounds to `[44.0, 22.0]` (South-West) and `[52.5, 40.5]` (North-East).
3. **Marker Clustering & Dynamic Pins (`src/utils/search/pinClustering.ts`)**:
   - Groups dense property pins at high zoom levels to maintain smooth 60 FPS scrolling and panning.
   - Custom SVG marker templates indicate price levels and highlight active selections.

---

## 6. Resilience & Fault Tolerance Strategy

| Failure Scenario | Detection Mechanism | Recovery Action |
| :--- | :--- | :--- |
| **Session Expiry (401)** | Axios response interceptor (`src/services/api.ts`) | Dispatches `auth:session-expired` event, clears local state, redirects to `/login` with return URL. |
| **Connection Drop** | Network event listener (`network:offline`) | Displays offline banner; TanStack Query pauses retries until connection resumes. |
| **Component Crash** | React Error Boundaries | Renders fallback UI, preventing full application crash. |
| **Tile Server Failure** | Leaflet `tileerror` event | Silently ignores failed external tiles while keeping vector markers and application UI operational. |
