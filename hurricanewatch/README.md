# MCTV Hurricane Watch

A self-contained signage feed board for the MCTV network: a live Mississippi tropical
tracker plus a live per-town traffic monitor for **Oxford, Tupelo, and Starkville**.

Live target: **`https://MCTVofMS.com/hurricanewatch`**

## What it shows

An auto-rotating full-screen deck (~14s per panel, with manual arrows/dots/keyboard,
pause-on-hover, and a live clock):

1. **Hurricane Watch** — color-coded threat banner (Take Cover / Get Ready / Stay Aware /
   All Clear), latest NWS headline, and the storm most relevant to the MS coast.
2. **Active Tropical Systems** — NHC storm cards: category, max wind, pressure, position,
   movement, and distance/bearing to the Mississippi coast.
3. **Statewide Alerts** — NWS warnings/watches/advisories for Mississippi, severity-sorted.
4–6. **Oxford / Tupelo / Starkville Traffic** — live Waze traffic map, a weather-impact-on-roads
   box, and a key-corridor watch list that flags corridors when a flood/wind/tropical alert
   covers that county.

A scrolling emergency ticker appears whenever tropical/flood/wind alerts are active.

## Data sources (all keyless)

| Feed | Source | Notes |
|------|--------|-------|
| Tropical alerts | `https://api.weather.gov/alerts/active?area=MS` | NWS GeoJSON, CORS-enabled |
| Active storms | `https://www.nhc.noaa.gov/CurrentStorms.json` | National Hurricane Center |
| Local traffic | `https://embed.waze.com/iframe?...&ct=livemap` | Waze live map, one iframe per town |

No API keys, no build step, no dependencies.

## Live vs. sample (why it degrades gracefully)

The page paints instantly from a **clearly-labeled SAMPLE dataset**, then attempts the live
fetches. When it reaches the internet (i.e. hosted on a normal web server such as
MCTVofMS.com) it flips every badge to **LIVE** and shows real data.

In a strict-CSP sandbox (e.g. the claude.ai Artifact preview) the external weather fetches and
Waze frames are blocked, so the board stays on labeled **SAMPLE** data and the traffic panels
show a labeled fallback tile. Nothing is faked as live — the badge and footer always tell the
truth about which mode is showing.

## Deploy

It's a single static file. Drop it wherever `/hurricanewatch` should resolve:

- **Static host / CDN / existing site:** copy `index.html` to the `hurricanewatch/` path so it
  serves at `MCTVofMS.com/hurricanewatch/`.
- **GitHub Pages / Netlify / Cloudflare Pages:** publish this folder; point the
  `MCTVofMS.com` domain (or a `/hurricanewatch` route) at it.
- **Digital signage player:** point the screen's browser at the hosted URL. It runs full-screen
  and rotates on its own — no interaction needed.

## Tuning

Open `index.html` and edit the `CONFIG` block near the top of the script:

- `SLIDE_MS` — dwell time per panel (default 14000 ms).
- `REFRESH_ALERTS_MS` / `REFRESH_STORMS_MS` — auto-refresh cadence.
- `TOWNS` — add/remove markets or adjust map center, zoom, and the key-corridor list.
