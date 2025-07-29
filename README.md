# Stochastic Orienteering Arc Routing Problem (SOARP) – Public Instances

This repository publishes JSON instance files for fogging vehicle routing for
dengue control under uncertainty on real street networks. The instances encode
present dengue case counts and multiple future scenarios.

## Contents

Instances are grouped by the number of future scenarios per instance:

- `03-scenarios/`: 3 future scenarios
- `05-scenarios/`: 5 future scenarios
- `09-scenarios/`: 9 future scenarios
- `50-scenarios/`: 50 future scenarios

Each directory contains multiple JSON files, one per instance.

## File naming

```
{as,ln}-<|V|>-<|A|>-<id>[-<k>scenarios].json
```

- `as` / `ln`: city abbreviation (Alto Santo, Limoeiro do Norte)
- `<|V|>`: number of vertices (nodes)
- `<|A|>`: number of arcs (directed street segments)
- `<id>`: instance identifier (1–6)
- `-<k>scenarios`: only present in `03-`, `05-`, `09-scenarios` directories

In `50-scenarios/`, filenames omit the `-50scenarios` suffix (implied by the
directory).

## JSON format

Each instance file has the following structure:

```
{
  "graph": {
    "nodes": [
      { "id": "0", "latitude": -5.52, "longitude": -38.27 },
      { "id": "1", "latitude": -5.53, "longitude": -38.26 }
    ],
    "arcs": [
      {
        "source": "0",
        "target": "1",
        "time": 12.34,
        "profits": [p0, p1, ..., pK]
      }
    ]
  },
  "max_time": T,
  "distributions": [w1, w2, ..., wK],
  "instance_type": "Stochastic"
}
```

- `graph.nodes`: list of nodes with unique string `id` and
  `(latitude, longitude)` coordinates.
- `graph.arcs`: directed arcs (street segments). Each arc has:
  - `time`: traversal time along the arc (consistent units within an instance).
  - `profits`: array of length `K+1` with dengue case “prizes”. Indexing rule:
    - `profits[0]` is the present prize.
    - For `i = 1..K`, `profits[i]` is the prize for scenario `i`.
- `max_time`: total time budget for the route.
- `distributions`: probabilities for the `K` future scenarios (sum to 1).
- `instance_type`: always `"Stochastic"` in this dataset.

## Quick start

Load and inspect an instance in Python:

```python
import json, pathlib
path = pathlib.Path('03-scenarios/as-253-710-2-3scenarios.json')
data = json.loads(path.read_text())

nodes = data['graph']['nodes']
arcs = data['graph']['arcs']
K = len(arcs[0]['profits']) - 1
weights = data['distributions']
T = data['max_time']

# Example: expected profit of first arc
present_plus_expected = arcs[0]['profits'][0] + sum(w * p for w, p in zip(weights, arcs[0]['profits'][1:]))
```

## Notes

- Instances use directed arcs; node coordinates are WGS84 lat/lon.
- Scenario counts are determined by directory and by `len(profits) - 1`.
