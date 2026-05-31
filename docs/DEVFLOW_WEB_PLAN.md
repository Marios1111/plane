# DEVFLOW_WEB_PLAN.md

## Goal

Turn the Plane web/backend repo into the owner-hosted DevFlow cloud service.

Users should experience a polished DevFlow SaaS product. They should not see self-hosting choices.

## What should change

### User-facing branding

Replace visible Plane branding with variable-driven DevFlow branding:

- app title
- login/sign-up screens
- onboarding
- email templates
- metadata
- public links
- admin instance display where safe
- mobile config output

### Login model

The product must have one normal login path:

```text
Sign in to DevFlow
```

No user-facing choice between:

- Plane Cloud
- self-hosted
- custom server URL

### Deployment

Owner-hosted only.

Add docs for VPS deployment, but keep those docs internal/admin-focused.

### Mobile readiness

Add/verify APIs needed by the new native iOS app.

## What should not change yet

- Do not rewrite the backend.
- Do not remove admin/God Mode.
- Do not remove cycles/modules/pages unless specifically requested.
- Do not add billing/subscriptions yet.
- Do not copy released Plane mobile UI.
- Do not migrate from pnpm to npm.
- Do not rename internal packages/modules until a dedicated rename phase.

## Suggested implementation phases

### Stage 0 — Baseline and audit

- Confirm branch.
- Confirm repo state.
- Confirm current build/test status.
- Create audit docs.
- Commit audit docs.

### Stage 1 — Brand config

- Add configurable product name/app URLs.
- Update env examples.
- Commit.

### Stage 2 — User-facing rebrand

- Replace safe visible copy.
- Keep internals stable.
- Commit.

### Stage 3 — Managed-cloud-only UX

- Remove self-host/cloud-choice user flows.
- Commit.

### Stage 4 — Mobile API

- Add/verify `/api/mobile-configs`.
- Document actual API contract.
- Commit.

### Stage 5 — Deployment hardening

- VPS Docker deployment docs/env examples.
- Commit.

### Stage 6 — Code review/security review

- Use CodeGraph and security tools if available.
- Fix findings.
- Commit fixes.

### Stage 7 — Optional internal rename feasibility

Only after everything works, audit whether internal `@plane/*` renaming is worth it.

Do not do this by default.

## Manual questions before final production

- What domain will DevFlow use?
- What SMTP provider will be used?
- Will public sign-up be open or invite-only?
- Will workspaces be created by users or only admins?
- Will AI features be enabled?
- Will source be public GitHub or downloadable zip for AGPL compliance?
