# ARCHITECTURE.md — DevFlow Web/Backend

## Purpose

This repository powers the owner-hosted DevFlow cloud service.

It is based on the Plane monorepo architecture, but the product direction is different:

- Plane supports cloud/self-host messaging.
- DevFlow is hosted by the owner and presented to users as one managed service.
- End users should not choose or configure self-hosted backends.

## System context

```text
+-------------------------+
| DevFlow iOS App         |
| Native SwiftUI client   |
+-----------+-------------+
            |
            | HTTPS JSON API
            v
+-------------------------+
| DevFlow Web/API Domain  |
| https://devflow.example |
+-----------+-------------+
            |
            v
+-------------------------+
| Proxy / Routing Layer   |
+------+-------+----------+
       |       |
       |       +------------------------+
       v                                v
+-------------+                 +-------------+
| Web App     |                 | API Server  |
| React stack |                 | Django/DRF  |
+-------------+                 +------+------+
                                      |
                                      v
                  +-------------------+-------------------+
                  |                                       |
                  v                                       v
              PostgreSQL                              Redis/Valkey

Additional services:
- admin
- space
- live
- worker
- beat-worker
- migrator
- RabbitMQ
- MinIO/S3
```

## Hosting architecture

Target:

```text
VPS
Docker Compose
HTTPS reverse proxy
PostgreSQL persistent volume
MinIO/S3 persistent uploads
Redis/Valkey
RabbitMQ
SMTP provider
```

Recommended VPS:

```text
Minimum practical: 2 vCPU / 4 GB RAM / 50 GB SSD
Better: 4 vCPU / 8 GB RAM / 80+ GB SSD
```

The service is not a static site. It is a multi-container application.

## Public URLs

Recommended production shape:

```text
https://devflow.example.com           main web app
https://devflow.example.com/api       backend API
https://devflow.example.com/auth      auth routes
https://devflow.example.com/god-mode  admin/God Mode
https://devflow.example.com/source    source/legal notice
```

## Major components

### Web app

User-facing browser UI.

### Admin/God Mode

Instance administration UI. Keep it protected.

### API

Django backend serving auth, users, workspaces, projects, work items/issues, cycles, modules, pages, comments, notifications, and settings.

### Workers

Background jobs for email, cleanup, notifications, imports/exports, and other async work.

### Live service

Realtime/collaboration-related service. Keep unless audit proves unused.

### Storage

MinIO or S3-compatible storage for uploads.

### Database

PostgreSQL is the source of truth.

### Cache/queue

Redis/Valkey and RabbitMQ are infrastructure dependencies.

## Package manager architecture

Keep pnpm.

Do not convert to npm unless a separate migration project is approved.

## Branding architecture

Brand config should be centralized.

Target env values:

```env
APP_NAME=DevFlow
APP_SLUG=devflow
COMPANY_NAME=DevFlow
PUBLIC_APP_URL=https://devflow.example.com
PUBLIC_API_URL=https://devflow.example.com
SUPPORT_EMAIL=support@devflow.example.com
LEGAL_SOURCE_URL=https://devflow.example.com/source
```

Avoid broad internal renaming during early phases:

- `plane` Python modules can remain.
- `@plane/*` packages can remain.
- Docker service names can remain initially.
- Database table/model names can remain.
- API internals can remain `issues`.

User-facing names should become DevFlow.

A deeper internal rename can be considered later in a dedicated branch.

## Product model

There are two separate concepts:

### Internal hosting

The owner self-hosts on a VPS.

This appears in deployment/admin docs only.

### End-user experience

Users see one managed service:

```text
Sign in to DevFlow
```

They should not see:

```text
Cloud sign in
Self hosted sign in
Enter server URL
Plane Cloud
```

## Mobile API boundary

The native iOS app should use a clean API contract.

Recommended mobile config endpoint:

```http
GET /api/mobile-configs
```

Recommended first mobile routes:

```http
POST /auth/sign-in/
POST /auth/sign-up/
GET  /api/users/me/
GET  /api/users/me/workspaces/
GET  /api/workspaces/
POST /api/workspaces/
GET  /api/workspaces/{slug}/projects/
POST /api/workspaces/{slug}/projects/
GET  /api/workspaces/{slug}/projects/{project_id}/issues/
POST /api/workspaces/{slug}/projects/{project_id}/issues/
GET  /api/workspaces/{slug}/projects/{project_id}/issues/{issue_id}/
PATCH /api/workspaces/{slug}/projects/{project_id}/issues/{issue_id}/
```

The app can call backend issues “work items”.

## Security architecture

Required:

- HTTPS only in production.
- Correct CORS origins.
- No wildcard production CORS.
- Strong generated secrets.
- No default production passwords.
- SMTP credentials in env only.
- OAuth credentials in env only.
- No secrets in frontend or iOS app.
- Rate limits preserved.
- Admin routes protected.

## Source/license architecture

Because the service is based on AGPL code:

- Preserve license files.
- Preserve notices.
- Provide source to network users as required.
- Add `/source` or equivalent source notice page.
- Document changes.

## Change strategy

Do this in phases:

1. Audit and docs.
2. Add brand config.
3. Replace user-facing branding.
4. Remove user-facing self-host/cloud-choice language.
5. Add mobile config endpoint.
6. Document mobile API contract.
7. Validate deployment.
8. Run security/code review.
9. Build custom iOS app separately.
10. Consider optional internal rename only after the service is stable.
