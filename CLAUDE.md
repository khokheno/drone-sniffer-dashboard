# Drone Sniffer dashboard

## Documentation rule (always)
`README.md` is the project reference and must never go stale. In the **same change** as any edit to
what the page shows, the data paths it reads, login/session behaviour or deployment, update the
matching README section.

## Working rules
- This repo is **public** and pushing to `main` deploys to GitHub Pages immediately. Never commit
  secrets. The device write path is protected only by being unguessable, so do not write it in
  docs or comments beyond what `index.html` already needs.
- The page reads fields produced by `buildTelemetryBody()` in the firmware repo
  (khokheno/esp32-remoteid-sniffer). Change both sides together.
