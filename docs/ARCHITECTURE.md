# Architecture Decision Records (ADR)

## ADR 01: Client-Side Mapping Engine and Core UI Stack

### Status
Accepted

### Context
We require an interactive web interface to map, inspect, and manage student pickup stops across Cuenca, Ecuador. The interface must communicate geographic coordinates and stop registries to a Vehicle Routing Problem (VRP) backend engine.

### Decision
We chose **Vanilla JavaScript (ES6+), HTML5, and Leaflet.js** loaded via CDN, served initially through a lightweight static HTTP server.

Alternative evaluated: React/Vue with Mapbox GL or `react-leaflet`.

### Consequences

#### Positive:
* **No Build Overhead:** Zero Node.js or bundler configuration required for initial phases, speeding up prototyping.
* **Direct DOM/Canvas Interoperability:** Leaflet provides imperative APIs that integrate directly with browser events (`click`, `flyTo`) without Virtual DOM synchronization friction.
* **Open Ecosystem:** OpenStreetMap tile layers provide high-accuracy mapping of Cuenca without requiring proprietary billing accounts or API keys.

#### Negative:
* **Manual State Sync:** Application state (`students` collection) must be synchronized manually with the DOM and map instances.
* **Scalability Trade-off:** Complex UI additions will require strict architectural discipline or eventual migration to a component-driven framework if view complexity grows substantially.

### Scale Considerations & Performance Thresholds
* **Dataset Volume:** ~600–800 student stops and 32 bus route polylines.
* **Rendering Strategy:** Standard DOM markers (`L.marker`) become a bottleneck at >300 elements. We will transition stop rendering to Canvas-based layers (`L.circleMarker` with `preferCanvas: true`) or Leaflet marker clusters.
* **DOM Virtualization:** The lateral sidebar must implement filtering by bus/sector to avoid mounting hundreds of DOM nodes concurrently.
