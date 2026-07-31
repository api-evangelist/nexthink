---
name: Run a Nexthink NQL query
description: Authenticate and execute a saved Nexthink NQL query, returning rows as JSON or CSV, using the asynchronous export path for large result sets.
api: openapi/nexthink-nql-api-openapi.json
operations: [execute, export-post, status]
method: generated
generated: '2026-07-20'
---

# Run a Nexthink NQL query

Use the Nexthink NQL API to pull inventory / experience data by executing a
**saved** query (referenced by its `queryId`, e.g. `#my_query`).

## Prerequisites
- An OAuth client credential (Client ID + Client Secret) created under
  **Administration > API credentials** with the **NQL API** permission.
- Your instance name and region (`us` | `eu` | `pac` | `meta`).

## Steps
1. **Get a token.** POST to
   `https://{instance}-login.{region}.nexthink.cloud/oauth2/default/v1/token`
   with `Authorization: Basic base64(clientId:clientSecret)`,
   `grant_type=client_credentials`, and `scope=service:integration`.
   The `access_token` is a JWT valid for 900 seconds — cache and refresh it.
2. **Execute the query** (`execute`). POST `/api/v2/nql/execute` on
   `https://{instance}.api.{region}.nexthink.cloud` with
   `Authorization: Bearer <token>` and a body of
   `{ "queryId": "#my_query", "parameters": { ... } }`.
   Set `Accept: application/json` (default) or `text/csv`; set
   `x-utc-times: true` for UTC timestamps. A `queryId` must match
   `^#[a-z0-9_]{2,255}$`.
3. **For large datasets, export instead** (`export-post`). POST
   `/api/v1/nql/export` to stream results to S3; it returns an `exportId`.
4. **Poll for completion** (`status`). GET `/api/v1/nql/status/{exportId}`
   until the export is ready and a signed download URL is returned.

## Rules
- Handle `401` (token missing/expired — re-auth) and `403` (credential lacks the
  NQL permission) distinctly. `404` means the `queryId` does not exist.
- `406` means the `Accept` header is not `application/json`/`text/csv`.
- Errors return `{ message, code, source }` (not RFC 9457). See
  `errors/nexthink-problem-types.yml`.
- No idempotency-key contract; treat `execute` as a read. See
  `conventions/nexthink-conventions.yml`.
