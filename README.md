# Boulder Bus Live

A responsive live tracker for RTD buses in and around Boulder, Colorado. The app combines official RTD GTFS-Realtime vehicle positions, trip updates, and service alerts into a map-first interface with route filtering, vehicle search, bus details, stop links, geolocation, and automatic refresh.

> Archive status: this repository is the rebuildable source archive for the ChatGPT Site **Boulder Bus Live**. The original Site was still active when this archive was prepared. See [`docs/SITE_ARCHIVE.md`](docs/SITE_ARCHIVE.md) for provenance, version identifiers, and restoration notes.

## What it does

- Displays live RTD vehicle locations within a Boulder-area bounding box.
- Refreshes the feed every 30 seconds.
- Filters buses by route and by vehicle/search text.
- Shows selected-bus details and upcoming stop IDs.
- Links stop IDs to RTD Next Ride.
- Surfaces active RTD service alerts.
- Centers the map on the rider's location when permission is granted.
- Adapts between map and list views on smaller screens.
- Exposes a WebMCP `filter_boulder_buses` tool for supported clients.
- Degrades gracefully when optional trip-update or alert feeds fail.

## Data flow

The browser requests `/api/live`. The server fetches and decodes RTD's official Protocol Buffer GTFS-Realtime feeds, filters vehicle positions to the Boulder region, enriches them with trip updates and alerts when available, and returns JSON to the UI.

| Feed | Official RTD endpoint |
|---|---|
| Vehicle positions | `https://open-data.rtd-denver.com/files/gtfs-rt/rtd/VehiclePosition.pb` |
| Trip updates | `https://open-data.rtd-denver.com/files/gtfs-rt/rtd/TripUpdate.pb` |
| Service alerts | `https://open-data.rtd-denver.com/files/gtfs-rt/rtd/Alerts.pb` |

The live endpoint uses a Boulder-area bounding box of latitude `39.84–40.27` and longitude `-105.55–-104.98`. Vehicle-feed failure returns HTTP 503; optional enrichment-feed failures are tolerated. Responses are cached for 10 seconds with a 15-second shared-cache window.

## Stack

- React 19 + Next.js/Vinext
- TypeScript
- Leaflet + OpenStreetMap tiles
- `gtfs-realtime-bindings`
- Tailwind CSS / shadcn component foundation
- Cloudflare Workers runtime tooling
- pnpm 11, Node.js 22.13+

No database, object storage, plugins, or scheduled automations are required.

## Run locally

```bash
pnpm install
pnpm dev
```

Then open the local URL printed by the development server.

Quality checks:

```bash
pnpm lint
pnpm build
```

## Important source files

| Path | Purpose |
|---|---|
| `app/page.tsx` | Tracker UI, map/list behavior, filters, selection, refresh, geolocation, WebMCP integration |
| `app/api/live/route.ts` | GTFS-Realtime fetching, decoding, Boulder filtering, enrichment, and error handling |
| `app/globals.css` | Dark pine/yellow/mint visual system and muted map treatment |
| `app/layout.tsx` | App metadata and Leaflet stylesheet loading |
| `public/favicon.svg` | Bus-and-mountain favicon |
| `.openai/hosting.json` | Original ChatGPT Site project association |
| `docs/SITE_ARCHIVE.md` | Full archive provenance and restoration checklist |
| `docs/site-versions.json` | Machine-readable version/deployment record |

## Restore or redeploy

1. Clone this repository.
2. Install Node.js 22.13+ and pnpm 11.
3. Run `pnpm install`, `pnpm lint`, and `pnpm build`.
4. Deploy to a compatible Next.js/Vinext + Cloudflare Workers environment, or import the source into ChatGPT Sites.
5. Confirm the three RTD feed URLs are reachable from the deployment runtime.
6. Smoke-test route filtering, vehicle selection, alert display, geolocation, and 30-second refresh behavior.

The app has no private API keys. Its live accuracy depends on RTD publishing current GTFS-Realtime data and on the deployment runtime being able to reach RTD's open-data host.

## Visual language

The product uses a near-black pine background (`#06110f`), RTD-inspired yellow (`#ffcc33`), mint accents, compact transit cards, and desaturated OpenStreetMap tiles. Preserve high contrast, touch-friendly controls, and the mobile map/list switch when extending the interface.

## License and upstream assets

This archive includes third-party starter components and vendor license files already present in the Site source. Review those files before redistributing or relicensing the project.
