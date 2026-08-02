---
name: Configure DPM alerts and destinations
description: List, create, and update threshold or event alerts in VividCortex DPM and route them to notification destinations.
api: openapi/vividcortex-openapi.yml
operations: [listAlerts, createAlert, updateAlert, getAlertConfig, listDestinations, listIntegrations]
---

# Configure DPM alerts and destinations

Manage alerting in VividCortex / SolarWinds DPM programmatically.

## Auth
`Authorization: Bearer <API_TOKEN>` with an RBAC role permitting configuration changes. Base URL `https://app.vividcortex.com/api/v2`.

## Steps
1. **Review destinations** — `listDestinations` (`GET /config/destinations`) and `listIntegrations` (`GET /config/integrations`) to get the destination IDs (Slack, Opsgenie, PagerDuty, email, webhook) alerts can route to.
2. **List existing alerts** — `listAlerts` (`GET /config/alerts`).
3. **Create an alert** — `createAlert` (`POST /config/alerts`) with body fields `name`, `policy`, `type` (`1`=event, `2`=threshold), `destinations` (array of destination IDs), and `disabled` (`0`/`1`).
4. **Set the trigger config** — for a threshold alert, `updateAlertConfig` (`PUT /config/alerts/{id}/config`) with `metric`, `trigger`, `duration`, `reset`, `alert_level`, `hosts`/`host_types`. For an event alert, supply `categories`.
5. **Verify** — `getAlertConfig` (`GET /config/alerts/{id}/config`) to confirm the stored configuration.

## Conventions & errors
- Alert `type` is numeric (`1`=event, `2`=threshold); `alert_level` is `info`/`warn`/`crit`.
- `401`/`403` on auth/permission; `404` if the alert id does not exist. See errors/vividcortex-problem-types.yml.
