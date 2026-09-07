---
name: find-a-resource
description: Search the federated ARD index for an MCP server, skill, agent or API
  that can do a task, then narrow to the exact verified tool to call.
api: Neuronto ARD Registry API
operations:
- search_endpoint_search_post
- tools_endpoint_tools_post
- explore_endpoint_explore_post
generated: '2026-09-07'
method: generated
source: openapi/neuronto-openapi.json
---

# Find an agentic resource

No key, no signup. All calls are anonymous.

1. **Search for a service** — `POST https://neuronto.com/search` with
   `{"query":{"text":"<the task, in plain language>"},"federation":"auto"}`
   (`search_endpoint_search_post`). `federation: auto` also queries every other
   public ARD registry live and fuses the rankings; results report which
   registries carry each match. Set `"federation":"local"`-style off only when
   speed matters more than coverage.
2. **Narrow to a tool** — when you know the shape of the call you need, use
   `POST https://neuronto.com/tools` with `{"q":"<what the tool should do>"}`
   (`tools_endpoint_tools_post`). Every tool returned was read back from a live
   server's own `tools/list`, so the name and input schema are what the server
   actually exposes.
3. **Explore the index** — `POST https://neuronto.com/explore`
   (`explore_endpoint_explore_post`) returns facet counts (families, sources) over
   this registry's own index; it does not federate.

Rules that matter:

- Relevance scores are semantic only — never a trust or safety rating. "Verified"
  means reachability and capability were read from the server itself, not that it
  is trustworthy.
- Validation failures return a 422 `HTTPValidationError` envelope; read
  `detail[].loc` and `detail[].msg`.
- Watch `X-RateLimit-Remaining`; on 429 wait for the epoch second in
  `X-RateLimit-Reset`.

The same search is available as the MCP tools `find_resource` and `find_tool` on
`https://neuronto.com/mcp` (streamable-http, no auth).
