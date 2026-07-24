---
authors:
  - RecoveryBox Team
date:
  created: 2025-01-20
categories:
  - Tutorial
tags:
  - maps
  - offline
---

# Setting Up Offline Maps

One of RecoveryBox's core features is **fully offline map capability**. Here's how it works under the hood.

## Architecture

The mapping stack consists of three components:

1. **generate_map** — Downloads OSM data from geofabrik.de and creates MBTiles files
2. **tileserver-gl** — Renders and serves vector/raster map tiles from local MBTiles
3. **BRouter** — Calculates routes using local profile data

## Generating Map Data

The `generate_map` tool automates the process:

```bash
# Maps are generated during installation
# To regenerate or add regions:
generate_map --region france
```

The tool downloads PBF files, processes them with Planetiler, and outputs MBTiles that tileserver-gl can serve.

## Accessing Maps

Once installed, maps are available at:

- **BRouter**: `http://recovery.box/brouter`
- **Tileserver**: `http://recovery.box/tileserver`

Both services work entirely offline — no internet connection required.

!!! info "GPS Integration"
    If a GPS module is connected, RecoveryBox automatically overlays your position on the BRouter map.

## Supported Map Profiles

BRouter includes multiple routing profiles:

- `fastest` — Optimized for speed
- `shortest` — Minimal distance
- `mtb` — Mountain bike friendly
- `hiking` — Walking routes
