# Relations graph

Below is a Mermaid flowchart depicting current relations among theories in data/theories.json.

```mermaid
flowchart LR
  %% Nodes
  gw["Global Workspace Theory (GWT)"]
  gnw["Global Neuronal Workspace (GNW)"]
  rpt["Recurrent Processing Theory (RPT)"]
  iit["Integrated Information Theory (IIT)"]
  illu["Illusionism"]
  ai["Analytic Idealism"]
  pan["Panpsychism"]
  dam["Dual-Aspect Monism / Neutral Monism"]

  %% Edges (labels indicate relation type)
  gw ---|equivalence| gnw
  gw ---|opposition| rpt
  iit ---|compatibility| gw
  illu ---|opposition| ai
  pan ---|compatibility| dam
```

Notes
- "equivalence" indicates near-identity or implementation relation.
- "compatibility" and "opposition" are undirected conceptual relations.
- Update data/theories.json -> relations to expand this graph; we can regenerate on demand.

