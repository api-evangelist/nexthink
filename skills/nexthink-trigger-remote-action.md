---
name: Trigger a Nexthink remote action
description: Discover an API-enabled remote action and trigger it across a set of devices, then track the resulting request in NQL.
api: openapi/nexthink-remote-actions-api-openapi.json
operations: [getAllRemoteActions, getRemoteActionByNqlId, executeRA]
method: generated
generated: '2026-07-20'
---

# Trigger a Nexthink remote action

Run a remediation or data-collection script on endpoints via the Remote Actions
API.

## Prerequisites
- OAuth client credential with the **Remote Actions API** permission.
- The action must have `targeting.apiEnabled = true`.

## Steps
1. **Authenticate.** Obtain a bearer token as in
   `skills/nexthink-run-nql-query.md` (scope `service:integration`).
2. **List remote actions** (`getAllRemoteActions`). GET
   `/api/v1/act/remote-action` and pick one whose `targeting.apiEnabled` is
   `true`. Note its `id` / NQL id.
3. **(Optional) Inspect it** (`getRemoteActionByNqlId`). GET
   `/api/v1/act/remote-action/details?nql-id=<id>` to read required `inputs`,
   `runAs`, and platform support (`hasScriptWindows` / `hasScriptMacOs`).
4. **Trigger it** (`executeRA`). POST `/api/v1/act/execute` with
   `{ "remoteActionId": "...", "devices": ["<collectorId>", ...],
   "params": { ... }, "expiresInMinutes": 1440,
   "triggerInfo": { "externalSource": "...", "externalReference": "TICKET-123" } }`.
   `devices` accepts 1–10000 IDs; `expiresInMinutes` is 60–10080.
   The response `requestId` is used to query executions in NQL.
5. **Track results.** Query the execution status by `requestId` with the NQL API.

## Rules
- `400` = invalid request (see the error `code`); `403` = missing Remote Actions
  permission or the action is not API-enabled.
- Errors return `{ code, message }`. See `errors/nexthink-problem-types.yml`.
- Triggering runs scripts on real endpoints — this is a consequential write; do
  not retry blindly (no idempotency key). See
  `agentic-access/nexthink-agentic-access.yml`.
