---
name: Trigger a Nexthink workflow
description: Find a workflow and trigger its execution for target devices or users, optionally keyed by external identifiers.
api: openapi/nexthink-workflows-openapi.json
operations: [getAllWorkflows, getWorkflow, executeEA, executeEAWithExternalIds]
method: generated
generated: '2026-07-20'
---

# Trigger a Nexthink workflow

Kick off an automated Nexthink workflow through the Workflows API.

## Prerequisites
- OAuth client credential with the **Workflows API** permission.

## Steps
1. **Authenticate.** Obtain a bearer token (scope `service:integration`).
2. **List workflows** (`getAllWorkflows`). GET `/api/v1/workflows` and choose the
   target `workflowUuid`.
3. **(Optional) Read the definition** (`getWorkflow`). GET
   `/api/v1/workflows/details` to review required inputs before triggering.
4. **Trigger execution.** POST `/api/v1/workflows/execute` (`executeEA`), or use
   `/api/v2/workflows/execute` (`executeEAWithExternalIds`) when addressing
   targets by external identifiers. Supply the workflow id and any inputs.
5. **(Optional) Advance a manual step** (`triggerThinklet`). POST
   `/api/v1/workflows/workflows/{workflowUuid}/execution/{executionUuid}/trigger`.

## Rules
- `401`/`403` handled as elsewhere; `404` = unknown workflow.
- Errors return `{ code, message }`. See `errors/nexthink-problem-types.yml`.
- Triggering a workflow is a write with side effects; no idempotency key is
  documented. See `conventions/nexthink-conventions.yml`.
