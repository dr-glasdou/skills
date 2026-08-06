---
name: yaak-test-logging
description: Persist every Yaak MCP HTTP exchange to an auditable log file. Use whenever running interactive API tests through the Yaak MCP (yaak_send_http_request, yaak_create_http_request, etc.). Triggers on "test the endpoint", "hit the API", "run this request in Yaak".
---

# Yaak MCP API Test Logging

When you run API tests through the Yaak MCP, persist every exchange to `docs/api/logs/`
so test history is auditable.

## Behavior
- **DO NOT** ask before logging — write the file first, mention it in the reply.
- One file per exchange. Filename: `<ISO8601-timestamp>-<sanitized-endpoint>.json`
  (e.g. `2026-08-06T15-30-00Z-health-db.json`). Sanitize: lowercase, slashes → `-`,
  strip query string, drop leading `/api/v1`.
- File contents — a single JSON object:
```json
  {
    "request":  { "method": "GET", "url": "...", "headers": {}, "body": null },
    "response": { "status": 200, "headers": {}, "body": "..." },
    "yaak":     { "requestId": "...", "folderId": "..." }
  }
```
- Redact `Authorization`, `Cookie`, `Set-Cookie` and any header matching
  `/token|secret|password|api[_-]?key/i` before writing.
- If `docs/api/logs/` is missing, create it. Never delete or rotate existing
  files — append only.

## Skipped
- Bulk re-runs from CI or scripted suites — log only interactive agent tests.
