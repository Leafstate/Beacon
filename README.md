# Beacon

A single-file HTML dashboard that aggregates multiple [Statuspage](https://www.atlassian.com/software/statuspage)-powered status pages into one view. Track uptime and incidents across every service you depend on, in one place, entirely in your browser.

## Features

- **30-day incident timeline** — a merged row across all tracked sources plus one row per source, colored by the worst incident/maintenance active each day. Non-operational days link straight to the announcement on the provider's own status page.
- **Per-source status cards** — current status, active incidents (with the latest update snippet), and scheduled maintenance windows, grouped into compact date chips.
- **Add any Statuspage-powered page** — paste a domain like `status.openai.com` or `githubstatus.com` and Beacon validates it and auto-detects the display name.
- **Light/dark theme**, with a sensible default based on the visitor's system preference.
- **Auto-refresh** every 5 minutes, plus a manual refresh button.
- **No backend** — sources and theme preference persist to `localStorage`; each provider's public `/api/v2/*` endpoints are fetched directly from the browser (all Statuspage-hosted pages serve these with CORS enabled).

Ships pre-populated with a handful of default sources (HubSpot, Shopify, PandaDoc, JustCall, Asana, Zapier, NetSuite) — remove or add to taste.

## Usage

Beacon is a static, dependency-free HTML file. To run it:

1. Open [`index.html`](index.html) directly in a browser, **or**
2. Serve the repo with any static file server (needed if you want to enable GitHub Pages), e.g.:
   ```
   npx serve .
   ```

To track a new source, click **Add source** and enter its status page domain. To remove one, use the ✕ on its card (removal can be undone via the toast that appears).

## Structure

Beacon is intentionally a single HTML file with no build step, no dependencies, and no bundler. Internally it's organized as a set of clearly delimited, self-contained modules, each covering one concern:

| Module | Responsibility |
|---|---|
| `util.js` | HTML escaping, safe URL handling, date/relative-time formatting |
| `status.js` | Statuspage impact/indicator → label, color, and rank mapping |
| `sources.js` | Tracked-sources + theme persistence (`localStorage`), defaults |
| `api.js` | Fetches `summary.json`, `incidents.json`, and `scheduled-maintenances.json` from each source's public Statuspage API |
| `timeline.js` | Buckets incidents/maintenances into day-by-day cells and renders the 30-day timeline |
| `cards.js` | Renders each source's status card (favicon, status pill, incidents, maintenance chips) |
| `toast.js` | Lightweight toast notifications (e.g. undo on remove) |
| `modal.js` | The "Add a status page" dialog |
| `theme.js` | Light/dark theme toggle and persistence |
| `main.js` | Wires everything together: app state, render loop, refresh interval, event handlers |

All CSS lives in a single `<style>` block using CSS custom properties for theming (`--bg`, `--accent`, `--status-*`, etc.), so the two themes are defined once and referenced everywhere.

## How it works

Beacon talks to each source's public Statuspage v2 API:

- `GET /api/v2/summary.json` — current status + active incidents/maintenances (required; a source is marked unreachable if this fails)
- `GET /api/v2/incidents.json` — incident history, used to paint the timeline
- `GET /api/v2/scheduled-maintenances.json` — maintenance history/upcoming windows

Only pages built on Statuspage.io (or a Statuspage-compatible clone) work — Beacon detects this by whether these endpoints respond.

## Privacy

Nothing is sent to any server Beacon controls — there isn't one. Requests go straight from your browser to each tracked provider's own API, and your list of sources lives only in your browser's `localStorage`.

## License

No license has been set for this repository yet — all rights reserved by default until one is added.
