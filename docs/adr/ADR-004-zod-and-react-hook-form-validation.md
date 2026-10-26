# ADR-004: Form Management with React Hook Form and Zod Validation Schemas

## Status
Accepted

## Context
Rentify features complex multi-step forms:
- Property creation and editing (pricing, amenities, geo-coordinates, image upload URLs).
- Booking creation (check-in/check-out dates, guest counts, special requests).
- User profile updates and identity verification.

Managing these using standard React `useState` leads to:
1. Re-rendering the entire form tree on every single keystroke.
2. Inconsistent validation rules scattered across event handlers.
3. Divergence between TypeScript types and runtime validation rules.

## Decision
We adopt **React Hook Form (v7)** coupled with **Zod (v4)** via `@hookform/resolvers/zod`:
- Forms use uncontrolled inputs through `register()`, eliminating keystroke re-renders.
- Validation logic is centralized in declarative Zod schemas.
- TypeScript types are inferred directly from schemas (`type FormData = z.infer<typeof schema>`).

## Consequences

### Positive
- Exceptional rendering performance even with dozens of input fields.
- 100% single source of truth for runtime validation and static TypeScript types.
- Automatic integration with accessible error messages and focus management.

### Negative
- Requires developers to learn Zod schema syntax and Hook Form controller patterns for non-standard UI controls (e.g., custom date pickers).
