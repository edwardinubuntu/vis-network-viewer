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

```jsonc
{
  "title": "My Network",
  "nodes": [
    { "id": "a", "label": "Alice", "group": "hero", "bio": "Protagonist." },
    { "id": "b", "label": "Bob",   "group": "villain" }
  ],
  "edges": [
    { "from": "a", "to": "b", "label": "rivals" }
  ],
  "metadata": {
    "groups": {
      "hero":    "Heroes",
      "villain": "Villains"
    }
  },
  "options": {
    "nodes": { "size": 22 },
    "physics": { "barnesHut": { "springLength": 160 } }
  }
}
```

- `metadata.groups` — maps each group key to a human-readable label used on the filter buttons. Optional; falls back to the raw key if omitted.
- `options` — overrides default vis.js physics and node-size settings per file.

---

## Setup

### Create the files folder

Create a `files` folder in the root directory and drop JSON files into it. The frontend will load them on refresh and they will appear in the dropdown menu.

### How to start

Use any tool that supports an HTTP server. For Python:

```bash
python3 -m http.server 8002
```

Port 8002 can be changed as needed.
