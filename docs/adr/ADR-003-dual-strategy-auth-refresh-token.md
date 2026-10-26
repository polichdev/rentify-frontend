# ADR-003: Dual-Strategy Authentication: HttpOnly Cookies with Bearer Fallback

## Status
Accepted

## Context
Web client security requires protection against Cross-Site Scripting (XSS) credential theft. If JWT tokens are stored in `localStorage` or JavaScript-readable memory, an XSS vulnerability can expose user credentials. Conversely, strict cookie-only authentication complicates local development across different localhost ports and prevents native mobile or headless testing.

## Decision
We implement a **Dual-Strategy Authentication Model** controlled by the environment flag `VITE_USE_HTTP_ONLY_AUTH_COOKIE`:

1. **Production Mode (`USE_HTTP_ONLY_AUTH_COOKIE=true`)**:
   - Authentication tokens are issued as `HttpOnly; Secure; SameSite=Strict` cookies by Spring Boot.
   - JavaScript cannot read or exfiltrate tokens.
   - CSRF protection is enforced via Double-Submit Cookie pattern: the frontend extracts `XSRF-TOKEN` cookie and passes it via `X-XSRF-TOKEN` header on mutation requests (`POST`, `PUT`, `DELETE`).

2. **Development / Mobile Compatibility Mode (`USE_HTTP_ONLY_AUTH_COOKIE=false`)**:
   - Tokens are returned in JSON payloads and managed via `src/services/storage.ts`.
   - Sent via standard `Authorization: Bearer <token>` request header.

## Consequences

### Positive
- Production grade resistance against XSS token exfiltration.
- Cross-origin development simplicity when frontend and backend run on different domains during local staging.
- Unified Axios interceptor logic handles both modes transparently.

### Negative
- Requires maintaining two auth execution branches in `src/services/api.ts` and `src/contexts/AuthContext.tsx`.
