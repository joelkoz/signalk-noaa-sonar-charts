# signalk-noaa-sonar-charts

> [!WARNING]
> **This plugin is deprecated and no longer maintained.** Its three charts are now
> part of [Charts Provider Simple](https://github.com/dirkwa/signalk-charts-provider-simple)
> (version 2.12.0 or later), which serves them the same way: tiles are fetched from
> NOAA as you view them and kept, so the charts keep working offline.

## Moving to Charts Provider Simple

1. Install **Charts Provider Simple** from the Signal K App Store, or update it to
   2.12.0 or later.
2. In its **Online Charts** tab, under depth and seabed, press **Add** on each chart
   you use: **NOAA Hi-Res Relief**, **BlueTopo Relief** and **BlueTopo Depth Color**.
   They start at the same opacities this plugin used.
3. To hide land on the two BlueTopo charts, as this plugin did, open
   **Manage Charts**, choose the chart, then **Meta → Edit**, and tick
   **+Water only**. This adds a copy of the chart with the land cut away as its own
   layer. The first time, you are asked to confirm a one-time download of coastline
   data (about 1 GB, about 390 MB on disk).
4. To pre-load an area before going offline, press **Offline area** on the chart
   instead of using the `noaa-sonar-charts` command.
5. Uninstall this plugin, so the old and new charts don't both appear in
   Freeboard-SK. The new charts are separate layers, so turn them on and set their
   order in Freeboard-SK again.
6. Delete this plugin's data folder,
   `<signalk-config>/plugin-config-data/noaa-sonar-chart-provider/`. It holds the
   old tile caches and coastline data, which Charts Provider Simple does not use.

The rest of this page describes the plugin as it was last released.

---

![screenshot](https://raw.githubusercontent.com/joelkoz/signalk-noaa-sonar-charts/main/docs/screenshots/noaa-sonar-charts-screenshot.jpg)

A Signal K chart provider for high resolution underwater relief charts around the eastern
United States and parts of the Caribbean. It adds three chart layers to Freeboard-SK and serves their tiles
from local caches, fetching any missing tile from NOAA on demand and saving it 
so the charts **work offline** from whatever you've already viewed.

| Chart | Description |
|---|---|
| **NOAA Hi-Res Relief** | High resolution relief (coverage limited) |
| **BlueTopo Relief** | Mid resolution underwater relief |
| **BlueTopo Depth Color** | Colorized depth indicator |

The layers are pre-baked with set opacities so they should play well with other layers with minimal configuration. The two BlueTopo layers have **land masked out** (made transparent) so they don't
cover the land features of your base chart. If you have any difficulties, change the stack order. The HiRes relief is best when its the top layer.

## Coverage area
![coverage](https://raw.githubusercontent.com/joelkoz/signalk-noaa-sonar-charts/main/bluetopo-coverage.jpg)

## Requirements

- Signal K server with Freeboard-SK v2 or later.
- Node.js ≥ 22.5.0.

## Installation

Locate the plugin in the Signal K App Store and install from there. 

Then **restart Signal K** and enable the plugin under **Server → Plugin Config →
"NOAA Sonar Chart Provider"**. The three charts appear in Freeboard's chart list.

## Configuration

| Setting | Default | Notes |
|---|---|---|
| Download missing tiles dynamically | on | Disable for offline viewing (serve only tiles already cached). |
| Baked layer opacity (advanced) | 0.75 / 0.50 / 0.30 | Per-layer opacity baked into the tiles so the layers stack well. Most users won't change these. |

Tile caches are stored in the plugin's data directory
(`<signalk-config>/plugin-config-data/noaa-sonar-chart-provider/`).

## Land masking (one-time download)

To mask out land, the plugin downloads a worldwide coastline dataset the first
time it runs (stored in the plugin's data directory). It's a large, one-time
download that happens automatically in the background; until it finishes, the
BlueTopo layers are shown without land masking. On a slow connection you can
pre-build this dataset on another computer and copy it over — see
[AGENTS.md](AGENTS.md).

## Pre-loading an area (optional)

By default, tiles are fetched as you view them. To pre-load an area in advance
(e.g. before heading offline), use the bundled `noaa-sonar-charts` command from
your Signal K configuration directory:

```bash
cd ~/.signalk
npx noaa-sonar-charts --bbox -82.0 24.4 -80.05 25.6
```

`--bbox` is **west south east north** in decimal degrees (WGS84) — west/east are
longitude, south/north are latitude, and western longitudes / southern latitudes
are negative. The example above covers the Florida Keys.

It pre-loads all three layers by default. Choose layers with `+`/`-` (default
`+all`): e.g. `-all +color` for only Depth Color, or `+all -hi` to skip the
hi-res layer. Other options: `--min-zoom`, `--max-zoom`, `--workers`, `--dir`.
Run `npx noaa-sonar-charts` with no arguments for full help.

It is resumable and additive — re-run it to extend the area or add zoom levels.

## Data sources & attribution

- NOAA NCEI bathymetric sonar (BAG hillshade subsets).
- NOAA Office of Coast Survey **BlueTopo** via nowCOAST.
- Coastline: OpenStreetMap land polygons (© OpenStreetMap contributors, ODbL).

NOAA data is in the public domain. **This plugin is not for navigation.**

## License

Apache-2.0.

Developers: see [AGENTS.md](AGENTS.md) for architecture, build/development setup,
and implementation notes.
