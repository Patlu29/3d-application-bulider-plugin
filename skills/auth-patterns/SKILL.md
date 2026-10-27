---
name: auth-patterns
description: Secure authentication and authorisation patterns for the backend of a 3D web application, covering managed providers, session cookies, password handling, rate limiting, CSRF, role checks and secret handling. Use only when the requirements include user accounts, sign-in or protected data.
user-invocable: false
---

# Authentication and authorisation

Only build authentication if a requirement needs it. Many 3D apps, such as product viewers and portfolios, need none.

## Prefer a managed provider

Use a managed identity provider or a well-maintained auth library rather than building sign-in from scratch. Record the choice in an ADR. Building it yourself means owning password storage, resets, email verification and account recovery.

## Sessions

- Use server-side sessions or short-lived tokens in cookies set with `HttpOnly`, `Secure` and `SameSite=Lax` (or `Strict`)
- Never store tokens in `localStorage` or `sessionStorage`; any injected script can read them
- Expire sessions after inactivity and on sign-out, and invalidate them on password change

## If you must handle passwords

- Hash with Argon2id, or bcrypt as a fallback; never store or log plain passwords
- Require a minimum of 12 characters; check against a list of breached passwords
- Use the same response for "unknown email" and "wrong password"

## Protecting endpoints

- Rate limit sign-in, sign-up and password reset by IP address and by account
- Protect state-changing requests from CSRF: `SameSite` cookies plus a CSRF token or an origin check
- Check authorisation on every request on the server; never trust the client to hide what a user may not do
- Check ownership as well as role: a user may edit only their own scenes

## Roles

Keep roles simple and explicit:

| Role | Can |
|---|---|
| viewer | Read public scenes |
| user | Create and edit their own scenes |
| admin | Manage all scenes and users |

## Secrets

- Keep secrets in environment variables or a secrets manager, never in code or committed files
- List required secrets in `server/.env.example` and `docs/deployment.md` without their values
- Never log tokens, cookies, passwords or full request bodies from auth routes

## Checklist before handoff

- [ ] No secrets in the repository
- [ ] Auth cookies are `HttpOnly`, `Secure` and `SameSite`
- [ ] Every protected route checks authorisation on the server
- [ ] Rate limiting is applied to auth routes
- [ ] Tests cover access by a signed-out user and by another user's account
