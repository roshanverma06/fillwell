# ADR-005: Google sign-in via chrome.identity + backend JWT

**Status:** Accepted · **Date:** 2026-11-14

## Context
Users need accounts so their data syncs and can be exported or deleted. Building password sign-in means storing passwords, password resets and more attack surface.

## Decision
- The extension uses `chrome.identity.launchWebAuthFlow` with **Google OAuth** (OpenID Connect).
- The backend verifies the Google ID token and issues its **own short-lived access JWT (15 min)** plus a **rotating refresh token**.
- Tokens are stored in `chrome.storage.local`, never in page context.

## Alternatives considered
- **Email + password:** more work and more risk.
- **Hosted auth (Auth0, Cognito, Clerk):** solid, but an added dependency and cost; easy to adopt later if we need more sign-in methods.

## Consequences
- **Benefit:** No passwords stored; fast onboarding.
- **Trade-off:** Google-only sign-in in MVP. Other methods can come later.
