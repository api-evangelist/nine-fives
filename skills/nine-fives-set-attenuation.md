---
name: Set RF attenuation on a Nine Fives attenuator
description: Read and set the attenuation setpoint (and boot-time default) on a Nine Fives POE-ATTEN-6G programmable RF attenuator over its local JSON REST API.
api: openapi/nine-fives-attenuator-openapi.yml
operations: [getSystemStatus, getAttenuation, setAttenuation, getStartupAttenuation, setStartupAttenuation]
---

# Set RF attenuation on a Nine Fives attenuator

The attenuator serves a JSON REST API on **port 80** at `http://<device-ip>`
(default Ethernet fallback `192.168.0.95`, USB-C `192.168.42.95`). No
authentication — the device must be reachable on the local test network.

## Steps

1. **Confirm the device** — `getSystemStatus` (`GET /api/system/status`) and check
   `model` / `serial` / `temperature`, and that `status` reads `Connected via eth`
   or `Connected via usb`.
2. **Read the current setpoint** — `getAttenuation` (`GET /api/attenuator`) returns
   `{"setpoint": <dB>}`.
3. **Set the attenuation** — `setAttenuation` (`POST /api/attenuator`) with body
   `{"setpoint": <dB>}`. Valid range **0 to 95.25 dB in 0.25 dB steps**. Values out
   of range or off-step return **500**; a malformed body returns **400**.
4. **(Optional) Set the boot default** — `setStartupAttenuation`
   (`POST /api/attenuator/startup`) with `{"startup_setpoint": <dB>}` persists the
   value applied on every power-up; read it back with `getStartupAttenuation`.

## Rules

- Requests/responses are `application/json`; errors come back as **plain text** with
  the HTTP status conveying the class (see `errors/nine-fives-problem-types.yml`).
- There is **no idempotency key**; POST is last-write-wins and safe to repeat.
- Round the requested dB to the nearest 0.25 before sending to avoid a 500.
