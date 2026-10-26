# Security Policy

## Supported Versions

We actively maintain and provide security patches for the following versions of Rentify Frontend:

| Version | Supported          |
| ------- | ------------------ |
| 1.x     | Yes |
| < 1.0   | No                |

---

## Reporting a Vulnerability

The Rentify engineering team takes the security of our platform and user data seriously. If you discover a potential vulnerability or security flaw, please report it responsibly.

### Where to Report
**Do not create public GitHub issues for security vulnerabilities.**

Instead, please submit a report directly via:
- **Email**: [security@rentify.ua](mailto:security@rentify.ua) or directly to the project maintainer at [polichdev@gmail.com](mailto:polichdev@gmail.com)
- **GitHub**: Use the private [Security Advisory](https://github.com/polichdev/rentify-frontend/security/advisories/new) reporting feature.

### Information to Include
To help us triage and resolve the issue quickly, please provide:
1. A detailed description of the vulnerability.
2. Step-by-step reproduction instructions or a minimal Proof of Concept (PoC).
3. The potential impact (e.g., token exfiltration, XSS injection, unauthorized booking mutation).
4. Any proposed fixes or mitigations.

### Response Timeline
- **Initial Response**: Within 48 hours.
- **Triage & Status Update**: Within 5 business days.
- **Remediation Release**: Depending on severity, critical patches will be prioritized and published within 7 days.

---

## Frontend Security Practices

Rentify Frontend adheres to strict client-side security measures:
- **Zero XSS Exposure**: Elimination of `dangerouslySetInnerHTML`, auto-escaping by React 19 JSX runtime, and strict form input sanitization with Zod.
- **Token Protection**: Support for `HttpOnly; Secure; SameSite=Strict` cookies in production mode to prevent JavaScript access to access and refresh tokens.
- **CSRF Protection**: Implementation of Double-Submit CSRF cookie validation for all non-safe HTTP methods (`POST`, `PUT`, `DELETE`).
- **Dependency Auditing**: Automated continuous auditing via `npm audit --omit=dev --audit-level=high`.
