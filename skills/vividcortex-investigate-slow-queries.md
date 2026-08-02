---
name: Investigate slow queries in VividCortex DPM
description: Find the heaviest queries on a host over a time window, inspect a query digest, and pull real execution samples to diagnose a slowdown.
api: openapi/vividcortex-openapi.yml
operations: [listHosts, getMetricSeries, listQueries, getQuery, getQuerySamples]
---

# Investigate slow queries

Diagnose a database slowdown in VividCortex / SolarWinds DPM by ranking query load, then drilling into the worst offender's samples.

## Auth
All calls use `Authorization: Bearer <API_TOKEN>` (per-environment token from DPM Settings → API Tokens). Base URL `https://app.vividcortex.com/api/v2`. Time bounds `from`/`until` accept relative seconds (e.g. `-3600`, `0`) or absolute Unix timestamps.

## Steps
1. **Identify the host** — call `listHosts` (`GET /hosts?from=-3600&until=0&nest=tags`) and grab the numeric `id` of the host to investigate.
2. **Rank query load** — call `getMetricSeries` (`GET /metrics/series`) with `metrics=host.queries.*.*.time_us`, `rank=1`, `limit=10`, and the `host` id to get the top-N heaviest query metrics over the window.
3. **List queries** — call `listQueries` (`GET /queries?from=-3600&until=0&host=<id>&filter=^select&limit=25`) to enumerate recently-seen query digests, filtering by text if needed.
4. **Inspect a digest** — call `getQuery` (`GET /queries/{queryId}?tags=1`) for the suspect query id to read its normalized digest and tags.
5. **Pull samples** — call `getQuerySamples` (`GET /queries/samples?query=<base10 id>&host=<id>&from=-3600&until=0&limit=5`). Remember the `text` field is **base64-encoded** — decode it to read the actual SQL and plan.

## Conventions & errors
- List responses wrap results in a top-level `data` array.
- Pagination: use `limit` + `offset`; the `x-vc-meta-more` response header signals more results.
- `401` = missing/invalid token, `403` = token role lacks permission. See errors/vividcortex-problem-types.yml.
