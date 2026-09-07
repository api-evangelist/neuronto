---
name: audit-discoverability
description: Audit whether AI agents can discover a domain, which registries return
  it, and where it ranks across the federation.
api: Neuronto ARD Registry API
operations:
- audit_endpoint_audit_post
- doctor_endpoint_doctor_post
- insights_endpoint_insights_get
- web_position_endpoint_web_position_get
generated: '2026-09-07'
method: generated
source: openapi/neuronto-openapi.json + https://neuronto.com/pricing
---

# Audit a domain's agent discoverability

Free, anonymous, works on any domain — including one you do not own.

1. **Audit** — `POST https://neuronto.com/audit` with `{"domain":"example.com"}`
   (`audit_endpoint_audit_post`). Reports which registries return the domain and
   whether this index can index it now. This is the expensive call: roughly 25
   outbound requests each, rate-limited to **30/hour anonymous** (observed live:
   `X-RateLimit-Limit: 30`).
2. **Diagnose** — `POST https://neuronto.com/doctor` (`doctor_endpoint_doctor_post`)
   for what is broken or missing on the domain's discovery surface.
3. **Rank** — `GET https://neuronto.com/insights` (`insights_endpoint_insights_get`)
   for where the domain ranks across every public ARD registry, who outranks it on
   its own queries, and share of voice (7 days of history anonymous, 30 with a
   verified key).
4. **Web position** — `GET https://neuronto.com/web-position`
   (`web_position_endpoint_web_position_get`) for where it ranks on the open web
   for the phrasings people actually use — what the models answering agents read.

Rules that matter:

- Budget audits: at ~25 outbound requests each they exhaust the 30/hour anonymous
  allowance fastest of any operation. On 429, wait for `X-RateLimit-Reset`.
- Every usage figure is a count somebody else publishes and can be checked with
  curl; the provider deliberately publishes no search-volume numbers.
