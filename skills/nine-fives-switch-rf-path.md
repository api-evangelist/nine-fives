---
name: Switch the RF path on a Nine Fives SPDT switch
description: Read and set the RF path (RF0/RF1) on a Nine Fives POE-SWITCH-6G programmable SPDT switch over its local JSON REST API.
api: openapi/nine-fives-switch-openapi.yml
operations: [getSystemStatus, getSwitchState, setSwitchState]
---

# Switch the RF path on a Nine Fives SPDT switch

The SPDT switch serves a JSON REST API on **port 80** at `http://<device-ip>`
(default Ethernet fallback `192.168.0.95`, USB-C `192.168.42.95`). No
authentication — reachable on the local test network only.

## Steps

1. **Confirm the device** — `getSystemStatus` (`GET /api/system/status`); verify
   `model`, `serial`, and connection `status`.
2. **Read the current path** — `getSwitchState` (`GET /api/switch`) returns
   `{"state": 0}` where **0 = RF0** and **1 = RF1**.
3. **Set the path** — `setSwitchState` (`POST /api/switch`) with `{"state": 0}` or
   `{"state": 1}`. Any value other than 0 or 1 returns **400**; a hardware failure
   to actuate returns **500**.

## Rules

- Requests/responses are `application/json`; errors are **plain-text** messages
  (see `errors/nine-fives-problem-types.yml`).
- No idempotency key is used; re-issuing the same state is safe.
- For automated test racks, drive the switch and the attenuator together — set the
  path first, then the attenuation setpoint.
