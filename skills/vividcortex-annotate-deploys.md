---
name: Annotate deployments on the DPM timeline
description: Record a deployment (or other operational change) as an event on the VividCortex timeline and read events back to correlate with performance changes.
api: openapi/vividcortex-openapi.yml
operations: [listHosts, createEvent, listEvents]
---

# Annotate deployments on the DPM timeline

Correlate performance shifts with releases by writing deploy markers into VividCortex / SolarWinds DPM.

## Auth
`Authorization: Bearer <API_TOKEN>`. Base URL `https://app.vividcortex.com/api/v2`.

## Steps
1. **(Optional) resolve the host** — `listHosts` (`GET /hosts`) to get the numeric host `id` if the event should be scoped to one host.
2. **Create the event** — `createEvent` (`POST /events`) with a JSON body: required `start` (Unix timestamp), `type` (e.g. `deploy`), and `message`; optional `host`, `duration`, and `level` (`info`/`warn`/`crit`).
   ```json
   { "start": 1462301121, "type": "deploy", "message": "Deployed release 1.4.2", "level": "info", "host": 203 }
   ```
3. **Read events back** — `listEvents` (`GET /events?from=-86400&until=0&host=<id>&addGlobal=1`) to confirm the marker and pull surrounding events. Use `addGlobal=1` to include non-host-scoped events.

## Conventions & errors
- `data`-wrapped responses; time bounds are relative seconds or Unix timestamps.
- `400` if a required event field is missing; `401`/`403` on auth. See errors/vividcortex-problem-types.yml.
