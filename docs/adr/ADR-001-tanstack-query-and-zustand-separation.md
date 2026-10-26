# ADR-001: Separation of Server State (TanStack Query) and Client State (Zustand)

## Status
Accepted

## Context
In early web applications, monolithic global stores (e.g. Redux / Redux Toolkit) held both asynchronous server data (properties, bookings, reviews) and local ephemeral UI state (active modal, search filters, form steps). This caused several problems:
1. Repetitive boilerplate for loading/error/success states.
2. Complicated manual cache invalidation, pagination sync, and memory garbage collection.
3. Over-rendering across unrelated components when cached lists updated.

## Decision
We separate application state strictly into:
1. **Server State**: Managed exclusively by **TanStack Query v5**. It handles automatic caching, refetching on window focus, garbage collection, and optimistic updates.
2. **Client State**: Managed by **Zustand v5**. Used exclusively for ephemeral UI controls that have no persistence on the backend (e.g., search drawer open/closed state, floating chat minimize/maximize).
3. **Ambient Session State**: Managed via native **React Context** (`AuthContext`, `ThemeContext`) for low-frequency updates.

## Consequences

### Positive
- Massive reduction in state management boilerplate.
- Automatic deduplication of identical HTTP requests across components.
- Cleaner component code: queries provide declarative `{ data, isLoading, error }` primitives.
- Deterministic cache invalidation using structured query key factories.

### Negative
- Developers must understand the boundary between server state and client state.
- Multiple state libraries in dependencies (TanStack Query + Zustand), though both have small bundle footprints.
