# VIS-NETWORK-VIEWER

An interactive HTML viewer built with vis.js for exploring relationship networks defined in JSON files.

## Features

### Data Loading

- **Auto-scan**: On startup, the app scans the `./files/` directory and populates a dropdown with every `.json` file found — no configuration needed.
- **Hot-reload**: A refresh button re-fetches the current file with a cache-busting timestamp so you can iterate on your data without restarting the server.

### Graph Interaction

- **Node click → highlight**: Clicking a node dims all unrelated nodes and edges, keeping only the selected node and its direct neighbours at full opacity.
- **Canvas click → reset**: Clicking empty space restores all nodes and edges to their default appearance.
- **Group filter buttons**: When a JSON file defines node groups, a row of pill buttons is automatically generated above the graph — one per unique group. Clicking a button highlights every node in that group; clicking the active button again resets the view.
- **Zoom controls**: Dedicated zoom-in / zoom-out buttons animate the viewport scale smoothly.
- **Node spacing controls**: Expand / collapse buttons adjust the physics spring length in real time, letting you spread or tighten the layout without a full re-render.

### Node Detail Panel

- Clicking a node opens a side panel showing its **label**, **group badge**, and **bio / description** pulled from the JSON data.

### Theming

- **Dark / light mode toggle**: Switches the entire UI and the vis.js canvas colours simultaneously. The preference is persisted in `localStorage` and restored on the next visit.

### Responsive Layout

- Single-column on small screens; two-column (graph + info panel) on `lg` and wider.
- The graph canvas height scales with the viewport (`78 vh` on `lg`, `82 vh` on `1440 px+`) up to a 1000 px cap.

---

## JSON File Format

A working sample is shipped at the repo root: [`example.json`](./example.json). To try it inside the viewer, copy it into the `files/` folder (`files/` is gitignored, so the sample lives at the root for version-control tracking):

```bash
cp example.json files/example.json
```

### Top-level keys

| Key               | Type   | Required | Description                                                                                          |
|-------------------|--------|----------|------------------------------------------------------------------------------------------------------|
| `title`           | string | optional | Shown as the page heading. Falls back to the filename (without `.json`) when omitted.                |
| `nodes`           | array  | required | List of node objects (see below).                                                                    |
| `edges`           | array  | required | List of edge objects (see below).                                                                    |
| `metadata.groups` | object | optional | Map of group key → human-readable label. Used for the group filter buttons and the badge in the side panel. Falls back to the raw key when omitted. |
| `options`         | object | optional | Per-file overrides for the vis.js render options. See **Options** below.                             |

### Node object

| Key       | Type           | Required | Description                                                                                     |
|-----------|----------------|----------|-------------------------------------------------------------------------------------------------|
| `id`      | string/number  | required | Unique identifier referenced by `edges[].from` and `edges[].to`.                                |
| `label`   | string         | optional | Text rendered on the node and used as the side-panel heading.                                   |
| `group`   | string         | optional | Group key. Drives the group filter buttons and the badge displayed in the side panel.           |
| `bio`     | string         | optional | Long description shown in the side panel. Preferred over `title`.                               |
| `title`   | string         | optional | Used by vis.js as a hover tooltip; also falls back as the side-panel description when `bio` is missing. |

Any other vis.js node property is passed through untouched. Common ones:

| Key          | Type    | Description                                                              |
|--------------|---------|--------------------------------------------------------------------------|
| `color`      | string/object | CSS colour string or `{ background, border, highlight }` object.   |
| `shape`      | string  | `dot`, `ellipse`, `box`, `diamond`, `triangle`, `star`, `hexagon`, `image`, etc. |
| `size`       | number  | Per-node size override (defaults come from `options.nodes.size`).        |
| `font`       | object  | `{ color, size, face, ... }`.                                            |
| `image`      | string  | Image URL (used with `shape: "image"`).                                  |
| `shadow`     | bool/object | Drop shadow toggle / config.                                         |
| `borderWidth`| number  | Border thickness in pixels.                                              |

See the [vis-network Node docs](https://visjs.github.io/vis-network/docs/network/nodes.html) for the full set.

### Edge object

| Key     | Type           | Required | Description                                                                                                                              |
|---------|----------------|----------|------------------------------------------------------------------------------------------------------------------------------------------|
| `from`  | string/number  | required | Source node `id`.                                                                                                                        |
| `to`    | string/number  | required | Target node `id`.                                                                                                                        |
| `label` | string         | optional | Short text rendered on the edge in the graph and shown as the bullet in the side panel's **Connected Edges** list.                       |
| `title` | string         | optional | Long description shown after an em-dash next to the edge label in the side panel. Hidden entirely when omitted. Also acts as a vis.js hover tooltip. |

Other vis.js edge properties are passed through. Common ones:

| Key      | Type           | Description                                                            |
|----------|----------------|------------------------------------------------------------------------|
| `arrows` | string/object  | `"to"`, `"from"`, `"to;from"`, `"middle"`, etc.                        |
| `dashes` | bool/array     | Dashed line.                                                           |
| `color`  | string/object  | Edge colour, including highlight/hover variants.                       |
| `width`  | number         | Line thickness in pixels.                                              |
| `smooth` | bool/object    | Curve type, e.g. `{ type: "continuous" }`.                             |
| `font`   | object         | Label font styling.                                                    |

See the [vis-network Edge docs](https://visjs.github.io/vis-network/docs/network/edges.html) for the full set.

### Options

| Key                                    | Type   | Default | Description                                                                                  |
|----------------------------------------|--------|---------|----------------------------------------------------------------------------------------------|
| `options.nodes.size`                   | number | `22`    | Default node size when an individual node does not specify its own `size`.                   |
| `options.physics.barnesHut.springLength` | number | `160` | Initial spacing between connected nodes. The on-screen Expand/Collapse buttons adjust this live. |

### Minimal example

```jsonc
{
  "title": "My Network",
  "nodes": [
    { "id": "a", "label": "Alice", "group": "hero",    "bio": "Protagonist." },
    { "id": "b", "label": "Bob",   "group": "villain" }
  ],
  "edges": [
    { "from": "a", "to": "b", "label": "rivals", "title": "Old college rivalry." }
  ],
  "metadata": {
    "groups": {
      "hero":    "Heroes",
      "villain": "Villains"
    }
  },
  "options": {
    "nodes":   { "size": 22 },
    "physics": { "barnesHut": { "springLength": 160 } }
  }
}
```

---

## Setup

### Create the files folder

Create a `files` folder in the root directory and drop JSON files into it. The frontend will load them on refresh and they will appear in the dropdown menu.

### How to start

#### Python (local)

Use any tool that supports an HTTP server. For Python:

```bash
python3 -m http.server 8002
```

Port 8002 can be changed as needed.

#### Docker

Build the image and run a container exposing port 8000:

```bash
# Build (no cache)
docker build --no-cache -t network-viewer .

# Run in the background
docker run -d -p 8000:8000 --name network-viewer network-viewer
```

The app will be available at `http://localhost:8000`.

To stop the container:

```bash
docker stop network-viewer && docker rm network-viewer
```
