# AGENT.md — DevFlow Web/Backend Repo

## Always-read rule

At the beginning of every task, read this `AGENT.md`.

If the task seems unrelated, still quickly inspect this file and follow any relevant constraints.

When using a coding agent, say explicitly:

```text
Read AGENT.md first and follow it unless a later user instruction explicitly overrides it.
```

## Tooling instruction

When available, use:

- CodeGraph for architecture/navigation/repo understanding.
- Superpowers optimized mode for planning and implementation.
- Security/code-review tools such as codex-security after meaningful code changes.

Do not invent tool output. If a tool is unavailable, continue normally and say it was unavailable.

## Product direction

This repository is a fork of `makeplane/plane` and will become the backend + web app for **DevFlow**.

Treat the product name as a configurable variable, not a permanent hardcoded string.

Default values:

```env
APP_NAME=DevFlow
APP_SLUG=devflow
COMPANY_NAME=DevFlow
PRIMARY_DOMAIN=devflow.example.com
PUBLIC_APP_URL=https://devflow.example.com
PUBLIC_API_URL=https://devflow.example.com
SUPPORT_EMAIL=support@devflow.example.com
LEGAL_SOURCE_URL=https://devflow.example.com/source
```

## Final product model

DevFlow is hosted by the owner on a VPS and presented to end users as a normal managed cloud service.

Correct model:

```text
User → DevFlow Web / DevFlow iOS App → Owner-hosted DevFlow backend
```

Wrong model:

```text
User chooses Plane Cloud vs self-hosted
User enters arbitrary backend URL
User hosts their own instance from the app
```

The owner self-hosts the infrastructure. Users do not.

## Legal compliance rule

This project is based on AGPL-licensed code.

Do not remove or weaken license/copyright notices unless there is a legally reviewed replacement strategy.

Required:

- Keep AGPL license files.
- Preserve required notices.
- Document modifications.
- Provide corresponding source to network users as required by AGPL.
- Keep a source/legal notice page or equivalent.

If asked to remove legal notices, stop and ask for legally reviewed replacement instructions instead of silently removing them.

## Non-negotiable engineering rules

1. Do not commit secrets.
2. Do not introduce user-facing self-host options.
3. Do not use Plane Cloud URLs in runtime behavior.
4. Do not migrate the monorepo from pnpm to npm.
5. Do not do dangerous blind repo-wide search-and-replace.
6. Do not break Docker Compose deployment.
7. Do not remove admin/God Mode.
8. Do not add billing/paywall logic in this phase.
9. Keep the product name configurable.
10. Commit after every coherent feature/phase.

## Internal package rename policy

Internal `@plane/*` packages, Python modules, service names, and database names may be renamed later **only in a dedicated rename phase**.

Do not rename them during early rebrand/API/deployment phases.

Reason:

- It risks breaking many imports.
- It creates large diffs.
- It makes upstream merges harder.
- It distracts from getting a working DevFlow service.

Allowed approach:

1. First make user-facing DevFlow branding work.
2. Stabilize deployment and mobile API.
3. Then audit whether internal package rename is worth it.
4. If yes, do it as a separate branch with full build/test validation.

## Package manager rule

Keep pnpm.

Do not migrate to npm unless explicitly requested after a separate feasibility audit.

Reason:

- root `package.json` declares pnpm as the package manager
- `pnpm-workspace.yaml` uses pnpm-specific workspace/catalog behavior
- Dockerfiles and build scripts expect the existing package manager model
- npm migration would create unnecessary risk

The operator may prefer npm personally, but this repo should remain pnpm-based.

## Repo architecture facts

Plane is a multi-service monorepo, not a simple static website.

Main services:

- `web`
- `admin`
- `space`
- `api`
- `worker`
- `beat-worker`
- `migrator`
- `live`
- `plane-db`
- `plane-redis`
- `plane-mq`
- `plane-minio`
- `proxy`

Main languages/technologies:

- TypeScript / JavaScript for frontend packages
- React / React Router / Vite
- Python / Django / Django REST Framework for backend
- PostgreSQL
- Redis/Valkey
- RabbitMQ
- MinIO/S3
- Docker / Docker Compose
- Shell / YAML
- pnpm / Turbo

## Hosting target

Host the whole stack on the owner’s VPS.

Production should look like:

```text
Browser / iOS app
        ↓
https://devflow.example.com
        ↓
VPS reverse proxy
        ↓
web + api + admin + workers + live
        ↓
Postgres + Redis + RabbitMQ + MinIO/S3
```

End users only see the public service URL.

## What to change

### Phase 1 — Audit

Create/update:

```text
DEVFLOW_REPO_AUDIT.md
```

Audit:

- apps
- packages
- Docker Compose services
- env files
- auth routes
- API routes
- frontend route structure
- public URLs
- emails
- branding
- source/license obligations
- user-visible Plane strings
- Plane Cloud references
- self-host references
- admin/God Mode
- deployment scripts
- iOS API requirements

### Phase 2 — Brand config

Add centralized brand configuration.

Prefer existing env/config patterns.

Target variables:

```env
APP_NAME=DevFlow
APP_SLUG=devflow
COMPANY_NAME=DevFlow
PUBLIC_APP_URL=https://devflow.example.com
PUBLIC_API_URL=https://devflow.example.com
SUPPORT_EMAIL=support@devflow.example.com
LEGAL_SOURCE_URL=https://devflow.example.com/source
```

Do not scatter hardcoded strings.

### Phase 3 — User-facing rebrand

Replace visible Plane branding in:

- browser title
- metadata
- auth screens
- onboarding
- emails
- admin display where safe
- public links
- help/support text
- mobile config response

Do not rename internal imports, package names, Django modules, database tables, or service names in this phase.

### Phase 4 — Managed-cloud-only UX

Remove or hide end-user self-host language.

Allowed:

- internal deployment docs
- admin-only infrastructure docs

Not allowed in normal user UX:

- “Self hosted sign in”
- “Enter server URL”
- “Plane Cloud”
- “Use your own instance”

### Phase 5 — Mobile API support

Add or verify:

```http
GET /api/mobile-configs
```

This endpoint must return safe public config only.

It must not expose secrets.

### Phase 6 — Deployment hardening

Create/update:

```text
DEVFLOW_CLOUD_DEPLOYMENT.md
```

Document:

- VPS setup
- Docker Compose
- domain/DNS
- HTTPS
- SMTP
- backups
- logs
- update flow
- rollback flow

## Auth policy

First supported:

- email/password

Optional later:

- magic links if SMTP is configured
- Google OAuth
- GitHub OAuth
- Apple Sign In

End users must not choose their own backend.

## Mobile relationship

The iOS app is a new native SwiftUI app.

Do not use the archived public Flutter mobile repo as the codebase.

The archived repo can be used only for API research.

## Terminology

User-facing DevFlow terminology:

| Backend / Plane term | DevFlow UI term |
|---|---|
| Issue | Work item |
| Issues | Work items |
| State | Status |
| Workspace | Workspace |
| Project | Project |
| Cycle | Cycle |
| Module | Module |
| Page | Page / Doc |

Backend internals can remain unchanged.

## Definition of done

The web/backend work is acceptable when:

- Web app loads as DevFlow.
- Users log into the owner-hosted service only.
- No user-facing Plane Cloud/self-host choice remains.
- Docker Compose deployment still works.
- Admin/God Mode still works.
- Signup/login works.
- User can create workspace/project/work item.
- `/api/mobile-configs` returns safe DevFlow config.
- iOS API contract is documented.
- No secrets are committed.
- Legal notices/source obligations are preserved.
