# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a static, single-file web application that visualizes the Mexico City Metro (CDMX) network with accessibility-aware route planning. No build tools, package managers, or dev servers are required.

## Running the App

Open `index.html` directly in a browser. For local development with CSV loading, serve from a local HTTP server to avoid CORS restrictions:

```bash
python -m http.server 8080
# or
npx serve .
```

## Architecture

Everything lives in `index.html` — embedded CSS, HTML structure, and JavaScript. Data is loaded from two CSV files at runtime:

- `data/nodos.csv` — stations: `id, Line, Type, Accessibility, Elevator, Electrical Stairs, People Affected, latitude, longitude`
- `data/aristas.csv` — connections: `source, target, Type, Line, PCD_Accessible`

### Key JavaScript Functions

| Function | Purpose |
|----------|---------|
| `load()` | Fetches and parses both CSVs asynchronously |
| `cL()` | Dispatches to the active layout algorithm |
| `lSch()` / `lGeo()` / `lCol()` / `lGrid()` / `lQuad()` / `lRad()` | Layout algorithms: schematic, geographic, 2-column, 2×2 grid, quadrants, radial |
| `fR(src, dst, accessibleOnly)` | Route-finding (Dijkstra-like), returns up to 3 alternative paths |
| `render()` / `renderRadial()` | SVG rendering for standard and radial views |
| `hav()` / `bearing()` | Haversine distance and compass bearing calculations |
| `initUI()` | Wires up all UI controls |

### Layout System

The SVG canvas supports all layouts via `cL()`. Switching layouts rerenders the SVG in-place without reloading data. Pan and zoom are implemented manually on the SVG element.

### Theming

CSS custom properties drive the full theme. Dark (default) uses blue/cyan accents; light uses sepia/gold. Toggle via the button in the header.

### Route Finding

`fR()` builds an adjacency list from `aristas.csv` edges, then runs shortest-path search. When `accessibleOnly` is true, only edges with `PCD_Accessible` set are traversed. Returns up to 3 paths ranked by total weight.
