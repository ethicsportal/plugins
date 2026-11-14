---
name: ethicsportal-api
description: Integrate with the EthicsPortal REST API and webhooks. Use when writing code that reads whistleblowing report metadata and deadlines, sets up organisations and whistleblowing channels, manages members, webhooks or documents, or verifies EthicsPortal webhook signatures.
---

# EthicsPortal API

The API exposes report metadata (status, assignment, deadlines), whistleblowing channel setup, members, documents, billing links and the audit trail. It never returns report content, messages, attachments or reporter identity.

## Read first

- Developer index, every page as Markdown: https://ethicsportal.eu/developers/llms.txt
- OpenAPI 3.1 schema with every operation, scope and token type: https://ethicsportal.eu/openapi.json
- All developer pages in one file: https://ethicsportal.eu/developers/llms-full.txt

## Rules

- Base URL: `https://secure.ethicsportal.eu`. JSON endpoints end in `.json`.
- Send `Authorization: Bearer <token>`. Read the token from the environment; never write it into code or logs.
- A personal token (`ep_pat_`) acts with its user's current role in each organisation. An organisation token (`ep_oat_`) belongs to one organisation and has no role. Each operation's `security` in the OpenAPI schema lists the token types it accepts and the scope it requires.
- Start with `GET /identity.json` to find the organisation IDs a token can reach. Treat IDs as opaque strings.
- Lists return `data` and `next_cursor` (discovery returns `organizations` instead of `data`). Repeat the request with `cursor` set to `next_cursor` until it is `null`, or follow the `Link: rel="next"` header.
- Send an `Idempotency-Key` header with every POST, and reuse it when retrying.
- The contract changes by addition. Ignore unknown response fields, unknown values in fixed-value fields and unknown webhook event types. Breaking changes are announced in the [changelog](https://ethicsportal.eu/developers/changelog.md) with `Deprecation` and `Sunset` headers.
- Errors have the shape `{"error": {"code": "..."}}`. On `429` or `503`, wait for the `Retry-After` interval. On `403 insufficient_scope`, the `WWW-Authenticate` header names the missing scope.
- Webhooks: verify `X-EthicsPortal-Signature` (HMAC-SHA256 over the timestamp, a full stop and the raw request bytes) before parsing JSON. Delivery is at least once and unordered, so deduplicate by the `X-EthicsPortal-Event-Id` header.

## Example

```sh
curl --fail-with-body \
  -H "Authorization: Bearer $ETHICSPORTAL_TOKEN" \
  "https://secure.ethicsportal.eu/organizations/$ETHICSPORTAL_ORGANIZATION_ID/whistleblower/reports.json?limit=50"
```

Tested examples: [fetch every report](https://ethicsportal.eu/developers/examples/pagination.md) and [verify a webhook](https://ethicsportal.eu/developers/examples/verify-webhook.md).
