# MOBILE_API_CONTRACT.md — DevFlow Backend ↔ Native iOS App

## Purpose

This file defines the API contract needed by the custom DevFlow iOS app.

The iOS app is not the archived Plane Flutter app. It is a new native client.

## Product assumption

The app connects only to the owner-hosted DevFlow service.

The app must not ask users for arbitrary self-hosted server URLs.

Production backend:

```text
https://devflow.example.com
```

The actual URL should be injected by build config.

## Auth

### Sign in

```http
POST /auth/sign-in/
Content-Type: application/json
```

Expected request must be confirmed from backend implementation.

Likely shape:

```json
{
  "email": "user@example.com",
  "password": "password"
}
```

Expected response shape must be confirmed before implementation.

Document:

- access token field
- refresh token field, if any
- user object, if included
- error response shape

### Sign up

Only enable if the product supports public sign-up.

```http
POST /auth/sign-up/
```

If DevFlow is invite-only, hide sign-up in iOS.

### Current user

```http
GET /api/users/me/
Authorization: Bearer <token>
```

## Mobile config

```http
GET /api/mobile-configs
```

Public, unauthenticated.

Must not expose secrets.

Expected response:

```json
{
  "appName": "DevFlow",
  "appSlug": "devflow",
  "publicAppUrl": "https://devflow.example.com",
  "publicApiUrl": "https://devflow.example.com",
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
```

## Workspaces

```http
GET /api/users/me/workspaces/
GET /api/workspaces/{slug}/
POST /api/workspaces/
```

The iOS MVP may initially only list and select workspaces.

## Projects

```http
GET /api/workspaces/{slug}/projects/
POST /api/workspaces/{slug}/projects/
GET /api/workspaces/{slug}/projects/{project_id}/
PATCH /api/workspaces/{slug}/projects/{project_id}/
```

## Work items

Backend may call these `issues`.

```http
GET /api/workspaces/{slug}/projects/{project_id}/issues/
POST /api/workspaces/{slug}/projects/{project_id}/issues/
GET /api/workspaces/{slug}/projects/{project_id}/issues/{issue_id}/
PATCH /api/workspaces/{slug}/projects/{project_id}/issues/{issue_id}/
DELETE /api/workspaces/{slug}/projects/{project_id}/issues/{issue_id}/
```

iOS UI should call them “work items”.

## Supporting data

The iOS app needs routes for:

- project members / assignees
- states/statuses
- labels
- priorities
- comments
- activity/history
- notifications

Document exact endpoints during backend audit.

## Error model

Create a consistent wrapper for iOS if backend responses vary.

Recommended iOS-side normalized error:

```swift
struct APIErrorResponse: Decodable {
    let code: String?
    let detail: String?
    let message: String?
    let errors: [String: [String]]?
}
```

## Versioning

Recommended header:

```http
X-DevFlow-Mobile-Version: 1
```

Optional future route:

```http
/api/mobile/v1/...
```

Do not introduce this unless necessary. Prefer existing Plane-compatible routes first.

## Open items

- Confirm auth response shape.
- Confirm refresh token flow.
- Confirm issue create payload.
- Confirm project create payload.
- Confirm comments/activity routes.
- Confirm notification routes.
