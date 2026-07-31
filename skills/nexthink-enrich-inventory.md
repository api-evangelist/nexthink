---
name: Enrich Nexthink inventory objects
description: Push external attributes onto Nexthink inventory objects (e.g. devices, users) via the Enrichment API.
api: openapi/nexthink-enrichment-api-openapi.json
operations: [enrichmentDataFields]
method: generated
generated: '2026-07-20'
---

# Enrich Nexthink inventory objects

Add third-party context (asset tags, CMDB fields, ticket metadata) to Nexthink
objects so it is queryable in NQL and usable by workflows.

## Prerequisites
- OAuth client credential with the **Enrichment API** permission.
- The custom fields to be enriched must be defined in Nexthink first.

## Steps
1. **Authenticate.** Obtain a bearer token (scope `service:integration`).
2. **Enrich fields** (`enrichmentDataFields`). POST
   `/api/v1/enrichment/data/fields` on
   `https://{instance}.api.{region}.nexthink.cloud` with
   `Authorization: Bearer <token>`, supplying the object identifier(s) and the
   field/value pairs to set per the Enrichment models.

## Rules
- `400` = malformed payload or unknown field (check the error `code`); `403` =
  missing Enrichment permission.
- This is a data-mutating write. See `agentic-access/nexthink-agentic-access.yml`
  and `errors/nexthink-problem-types.yml`.
