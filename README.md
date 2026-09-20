# Drone Sniffer live dashboard

The cloud dashboard for the ESP32 drone Remote ID sniffer. One static page that shows what the
device last reported, wherever you are, without needing to be on the device's WiFi.

- Live: https://khokheno.github.io/drone-sniffer-dashboard/
- Device firmware: [khokheno/esp32-remoteid-sniffer](https://github.com/khokheno/esp32-remoteid-sniffer) (private)
- Firmware releases: [khokheno/drone-sniffer-ota](https://github.com/khokheno/drone-sniffer-ota) (private)

> **Keep this file current.** Update it in the same change as any UI, data-path or deployment change.

## What it shows

- **Connection banner**: "Connected" when the newest push is under 150 s old, "Device appears
  offline" after that, or an error if the backend is unreachable.
- **Map** (OpenStreetMap tiles via Leaflet, no API key): the drone (green), the operator (blue) and
  the sniffer (orange). It zooms to fit whichever of the three currently have a position.
- **Counters**: beacons seen, vendor IEs, unknown OUIs.
- **Drone**: ID, position, altitude, how long ago it was seen, and a link to OpenStreetMap.
- **Operator**: position and age.
- **Sniffer GPS**: live or last known position, fix time, satellites in view and used.
- **Location history**: the last 10 permanent GPS fixes, newest first.
- **Footer**: device uptime at last push, firmware version, and the last OTA update (time and version).

All times are UTC on the wire and shown in the viewer's local time zone.

## How it works

Single file, `index.html`, no build. It uses the Firebase Web SDK (App, Auth, Realtime Database),
Leaflet from a CDN, and no other dependencies.

- **Login**: Firebase Authentication with email and password. The database rules require a signed-in
  user, so the page shows nothing without one. The page also forces a sign-out 8 hours after login
  (client-side session hygiene, not the real access control).
- **Data**: it listens live to two paths under `devices/<device path>/`:
  `status` (the latest snapshot the device overwrites every 60 s) and `locationHistory`
  (one permanent record per GPS fix, keyed `YYYYMMDDTHHMMSSZ`, read with `limitToLast(10)`).
- **Staleness**: Firebase only fires when data changes, so the page re-checks age itself every 15 s
  using the server-assigned `serverUpdatedAt`, not the device clock.
- The Firebase web config in the page is public by design. Access control is the login plus the
  database rules, so no secrets belong in this repo (it is public).

## Deploying

GitHub Pages serves `main` from the repository root. Pushing to `main` deploys within a minute or
two (there is no separate CI). Check the build with
`gh api repos/khokheno/drone-sniffer-dashboard/pages/builds/latest --jq .status`.

## Changing it

Edit `index.html`, push to `main`. To check JavaScript syntax before pushing, copy the inline
`<script>` block into a file and run `node --check` on it. Keep the device data schema in step with
the firmware: field names are produced by `buildTelemetryBody()` in the firmware repo.

## Documentation policy

Update this README in the same commit as any UI or data-path change. `CLAUDE.md` tells Claude Code
to do this automatically.
