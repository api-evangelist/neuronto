---
name: publish-and-verify
description: Get an MCP server or an ARD-publishing domain listed in the index, and
  track the submission until it verifies.
api: Neuronto ARD Registry API
operations:
- submit_endpoint_submit_post
- submit_status_submit_status__sid__get
- manifest_build_manifest_build_post
generated: '2026-09-07'
method: generated
source: openapi/neuronto-openapi.json + https://neuronto.com/llms.txt
---

# Publish a resource and verify it listed

No key, no signup, no allowlist. Nothing is taken on trust: an endpoint must
complete an MCP initialize handshake, a domain must serve a manifest that parses.

1. **(Optional) Build a manifest** — if the domain has no `/.well-known/ard.json`,
   `POST https://neuronto.com/manifest/build` (`manifest_build_manifest_build_post`)
   writes one from what the domain already publishes (an MCP server card, an
   OpenAPI document or an llms.txt is enough).
2. **Submit** — `POST https://neuronto.com/submit` (`submit_endpoint_submit_post`)
   with either `{"domain":"example.com"}` (both well-known paths are fetched and
   whatever parses is indexed in the same request) or
   `{"endpoint":"https://example.com/mcp"}` (its own `tools/list` is read back).
3. **Read the outcome** — a submission that verifies immediately returns 200. One
   that does not returns **202 with status "pending"**, a submission id and the
   evidence of what the endpoint returned; it is retried automatically for about
   two and a half days. Poll `GET https://neuronto.com/submit/status/{sid}`
   (`submit_status_submit_status__sid__get`).

Rules that matter:

- **One call is enough** — resubmitting the same domain or endpoint does not help
  and does not create duplicates (the submit operation is idempotent by resource
  identity).
- 400 means the body was neither a usable endpoint nor a domain; 404 means nothing
  could be fetched to verify.
- Anonymous allowance is 60 submissions/hour (`X-RateLimit-*` headers on every
  response); a DNS-verified domain key raises it to 300.
- The MCP tool `publish_resource` on `https://neuronto.com/mcp` does the same thing
  from inside a conversation.
