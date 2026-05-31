# prompt.md — Advanced coding-agent prompts for DevFlow web/backend

## How to use these prompts

Use these prompts in order.

Do not ask the agent to complete the entire rebrand in one pass.

You should clone/fork the repo yourself first, then paste these prompts from inside the repo.

Recommended setup before using the prompts:

```bash
git clone https://github.com/makeplane/plane.git devflow-web
cd devflow-web
git checkout -b devflow-cloud
```

Then copy these docs into the repo root:

```text
AGENT.md
ARCHITECTURE.md
DEVFLOW_WEB_PLAN.md
MOBILE_API_CONTRACT.md
prompt.md
```

## Global instruction to include with every prompt

```text
Before doing anything, read AGENT.md and follow it.
Use CodeGraph for repository understanding if available.
Use Superpowers optimized mode if available.
Commit after every coherent feature or completed stage.
Do not migrate pnpm to npm.
Do not create user-facing self-host flows.
Do not remove legal/license/copyright notices unless there is a legally reviewed replacement strategy.
If a requested action conflicts with AGENT.md, stop and explain the conflict.
```

---

# Stage 0 — Preflight and audit only

```text
Before doing anything, read AGENT.md and follow it.
Use CodeGraph for repository understanding if available.
Use Superpowers optimized mode if available.

You are working in a fork/clone of makeplane/plane.

Goal:
Audit the repository and create DevFlow planning docs. Do not implement runtime rebrand yet.

Product context:
This repo will become the web/backend for an owner-hosted managed cloud service called DevFlow. The product name must remain configurable because it may change later. End users do not self-host. They only log into the owner-hosted DevFlow service.

Rules:
- Do not migrate pnpm to npm.
- Do not rename internal packages/modules.
- Do not do broad search-and-replace.
- Do not remove legal/license/copyright notices.
- Do not change runtime code unless necessary for documentation setup.
- Commit after this stage.

Tasks:
1. Check git status and current branch.
2. Inspect architecture:
   - apps
   - packages
   - Docker Compose files
   - env examples
   - package manager config
   - frontend apps
   - backend API
   - auth routes
   - admin/God Mode
   - workspace/project/issue routes
   - email templates
   - Plane Cloud references
   - self-host references
   - legal/source files
3. Create or update:
   - DEVFLOW_REPO_AUDIT.md
   - DEVFLOW_CLOUD_DEPLOYMENT.md
   - LEGAL_SOURCE_NOTICE.md
   - MOBILE_API_CONTRACT.md
4. In DEVFLOW_REPO_AUDIT.md, include:
   - safe rebrand targets
   - risky/internal rename targets
   - exact files likely involved
   - current build/test commands
   - production blockers
   - mobile API gaps
5. In MOBILE_API_CONTRACT.md, document actual routes found, not guesses.
6. Check if /api/mobile-configs exists. If not, document how to add it.
7. Run only safe commands. Do not perform destructive operations.

Validation:
- Run formatting only if docs need it.
- Do not run expensive builds unless you judge it safe.

Commit:
Add DevFlow architecture audit docs
```

---

# Stage 1 — Brand config foundation

```text
Before doing anything, read AGENT.md and follow it.
Use CodeGraph for repository understanding if available.
Use Superpowers optimized mode if available.

Goal:
Add centralized configurable branding for the DevFlow fork.

Product context:
The owner hosts one managed DevFlow service. End users do not self-host and should not see self-host choices.

Rules:
- Product name must be variable-driven.
- Default APP_NAME is DevFlow.
- Do not rename internal @plane/* packages yet.
- Do not rename Python modules/database tables/service names yet.
- Do not migrate pnpm to npm.
- Do not remove legal/license/copyright notices.
- Commit after this stage.

Target env/config values:
APP_NAME=DevFlow
APP_SLUG=devflow
COMPANY_NAME=DevFlow
PUBLIC_APP_URL=https://devflow.example.com
PUBLIC_API_URL=https://devflow.example.com
SUPPORT_EMAIL=support@devflow.example.com
LEGAL_SOURCE_URL=https://devflow.example.com/source

Tasks:
1. Inspect existing frontend/backend config patterns.
2. Add the smallest clean brand config layer compatible with the existing architecture.
3. Add safe defaults so local dev still works.
4. Update env examples with safe placeholders.
5. Do not replace every “Plane” string yet.
6. Update DEVFLOW_REPO_AUDIT.md or a new BRANDING_NOTES.md explaining:
   - config files changed
   - how to change product name later
   - what was intentionally not renamed

Validation:
- Run relevant typecheck/build checks if feasible.
- Run docker compose config if feasible.
- Report failures clearly.

Commit:
Add configurable DevFlow brand settings
```

---

# Stage 2 — User-facing rebrand

```text
Before doing anything, read AGENT.md and follow it.
Use CodeGraph for repository understanding if available.
Use Superpowers optimized mode if available.

Goal:
Change safe user-facing branding from Plane to the configurable product name.

Critical product rule:
Users do not self-host. Users only log into the owner-hosted service.

Do not:
- migrate pnpm to npm
- rename @plane/* packages
- rename Python modules/database tables/service names
- break admin/God Mode
- remove legal/license/copyright notices
- break Docker Compose
- add billing/paywall
- do blind search-and-replace

Tasks:
1. Replace visible Plane branding with variable-driven APP_NAME where safe.
2. Update:
   - browser title
   - metadata
   - auth screens
   - onboarding text
   - email templates
   - public support links
   - visible product copy
3. Keep internal names stable.
4. Document every intentionally skipped “Plane” occurrence and why it was skipped.
5. Update LEGAL_SOURCE_NOTICE.md if needed.

Validation:
- Run frontend typecheck/build if feasible.
- Run backend tests only if backend logic changed.
- Run docker compose config if feasible.
- Report failures clearly.

Commit:
Rebrand user-facing DevFlow experience
```

---

# Stage 3 — Managed-cloud-only UX

```text
Before doing anything, read AGENT.md and follow it.
Use CodeGraph for repository understanding if available.
Use Superpowers optimized mode if available.

Goal:
Remove user-facing cloud-vs-self-host or arbitrary-server choices.

Product context:
The owner self-hosts DevFlow internally, but end users experience it as one managed cloud service.

Remove/hide from normal user UX:
- “Self hosted sign in”
- “Enter server URL”
- “Plane Cloud”
- “Use your own instance”
- any user-facing choice between cloud and self-hosted

Allowed:
- internal/admin deployment docs
- legal/source notices
- developer documentation that explains owner-hosted deployment

Tasks:
1. Search for user-facing self-host/cloud-choice strings.
2. Remove or replace them with managed-service copy.
3. Ensure login flow has one normal path:
   “Sign in to {APP_NAME}”
4. Keep internal deployment/admin docs intact.
5. Update docs with what changed.

Validation:
- Run relevant frontend checks.
- Manually inspect affected routes/screens if possible.
- Report any unresolved UX references.

Commit:
Remove user-facing self-host choices
```

---

# Stage 4 — Mobile config endpoint and API contract

```text
Before doing anything, read AGENT.md and follow it.
Use CodeGraph for repository understanding if available.
Use Superpowers optimized mode if available.

Goal:
Prepare the backend for a custom native iOS app.

Product rule:
The iOS app connects only to the owner-hosted DevFlow backend. It does not let users enter arbitrary server URLs.

Tasks:
1. Check whether GET /api/mobile-configs exists.
2. If missing, add it.
3. Make it public/unauthenticated.
4. Return safe public client config only.
5. Do not expose secrets.
6. Use brand/env config where appropriate.
7. Add tests for the endpoint.
8. Update MOBILE_API_CONTRACT.md with exact response shape and actual route details.
9. Confirm current routes and response assumptions for:
   - auth
   - current user
   - workspaces
   - projects
   - issues/work items
   - comments/activity
   - labels
   - states/statuses
   - notifications

Suggested response:
{
  "appName": "DevFlow",
  "appSlug": "devflow",
  "publicAppUrl": "https://devflow.example.com",
  "publicApiUrl": "https://devflow.example.com",
  "supportEmail": "support@devflow.example.com",
  "emailPasswordLogin": true,
  "magicLogin": true,
  "socialLogin": {
    "google": false,
    "github": false,
    "gitlab": false
  },
  "features": {
    "ai": false,
    "stickies": true,
    "pages": true,
    "cycles": true,
    "modules": true,
    "notifications": true
  },
  "limits": {
    "fileSizeLimit": 5242880
  }
}

Validation:
- Run backend tests for the endpoint.
- Run route/schema checks if available.
- Run docker compose config if feasible.

Commit:
Add DevFlow mobile config API
```

---

# Stage 5 — VPS deployment hardening

```text
Before doing anything, read AGENT.md and follow it.
Use CodeGraph for repository understanding if available.
Use Superpowers optimized mode if available.

Goal:
Prepare this repo for deployment as the owner-hosted DevFlow cloud service on a VPS.

Important:
This is not a static site. It is a multi-container Docker Compose app.

Tasks:
1. Audit current Docker Compose setup.
2. Create/update DEVFLOW_CLOUD_DEPLOYMENT.md with exact commands for:
   - Ubuntu VPS setup
   - Docker install
   - repo clone
   - env setup
   - domain/DNS
   - HTTPS
   - SMTP
   - MinIO/S3
   - Postgres backups
   - uploads backups
   - logs
   - update deployment
   - rollback
3. Add safe production env example files if helpful:
   - .env.example.devflow
   - apps/api/.env.example.devflow
4. Do not commit real secrets.
5. Do not change runtime architecture unless necessary.
6. Keep pnpm.
7. Keep Docker Compose.

Validation:
- Run docker compose config.
- If build is feasible, run a build and document results.
- Report unresolved production blockers.

Commit:
Document DevFlow VPS deployment
```

---

# Stage 6 — Code review and security review

```text
Before doing anything, read AGENT.md and follow it.
Use CodeGraph for repository understanding if available.
Use Superpowers optimized mode if available.
Use security/code-review tooling such as codex-security if available.

Goal:
Review the implemented DevFlow changes for correctness, security, and maintainability.

Scope:
Review all changes made so far in this branch.

Tasks:
1. Inspect git diff against the base branch.
2. Check for:
   - committed secrets
   - broken auth behavior
   - unsafe CORS
   - exposed private config
   - broken legal/source notices
   - hardcoded DevFlow strings where config should be used
   - remaining user-facing Plane Cloud/self-host choices
   - accidental internal package/module renames
   - Docker Compose breakage
   - risky changes to admin/God Mode
3. Run available tests/builds:
   - frontend checks
   - backend tests for touched code
   - docker compose config
4. Fix issues found.
5. Update docs with any remaining known risks.

Commit:
Review and harden DevFlow web changes
```

---

# Stage 7 — Optional internal rename feasibility audit only

```text
Before doing anything, read AGENT.md and follow it.
Use CodeGraph for repository understanding if available.
Use Superpowers optimized mode if available.

Goal:
Audit whether internal package/module renaming from Plane to DevFlow is worth doing.

Important:
Do not perform the rename in this stage. Audit only.

Tasks:
1. Identify internal names:
   - @plane/* packages
   - Python/Django modules
   - Docker service names
   - database tables/models
   - env vars
   - import aliases
   - public API paths
2. Classify each as:
   - safe to rename
   - risky to rename
   - should not rename
   - requires migration
3. Estimate effort and risk.
4. Explain impact on:
   - upstream merges
   - Docker
   - database migrations
   - frontend imports
   - backend imports
   - docs
   - mobile app
5. Produce INTERNAL_RENAME_FEASIBILITY.md.

Validation:
- Docs only.
- No runtime code changes.

Commit:
Audit optional internal DevFlow rename feasibility
```
