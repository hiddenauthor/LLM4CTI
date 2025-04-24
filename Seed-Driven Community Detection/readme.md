# Seed-Driven Community Detection

This module implements the **community detection** phase of the LLM4CTI framework, designed to identify cross-document semantic clusters of security entities (e.g., malware, threat actors, vulnerabilities) within the knowledge graph constructed from CTI articles.

LLM4CTI applies a **seed-based OSSE algorithm** to detect overlapping, multi-perspective communities that highlight shared exploit chains, coordinated threat activities, and reusable infrastructure components.

---

## 🔄 Workflow

The notebook `/Seed-Driven Community Detection.ipynb` implements the following steps:

### 1. Graph Preparation
- Load and optionally clean the aggregated knowledge graph.
- Convert to an undirected format suitable for community algorithms.
- Optionally remove noisy attributes (e.g., edge `source`) for fair analysis.

### 2. Typed Seed Selection
- Use `get_typed_seeds_for_osse()` to sample nodes of selected types:
  - `malware`, `threat-actor`, `attack-pattern`, `vulnerability`

### 3. Community Detection
- Apply `cdlib.algorithms.overlapping_seed_set_expansion()` (OSSE).
- Returns overlapping communities of semantically linked nodes.

### 4. Community Filtering & Analysis
- `analyze_community_edge_sources()` filters communities with edges from multiple articles.
- `compute_community_stats()` computes size, edge density, and source diversity.

### 5. Visualization
- `visualize_community_graph_no_overlap()` draws annotated subgraphs with edge labels and source-based coloring.

---

## 🚀 How to Use

### 1. Prepare Knowledge Graph

```python
import pandas as pd

df = pd.read_excel("RQ4.xlsx")
G_aggregated = aggregate_knowledge_graph(df, "knowledge graph", "keyindex")
```

### 2. Run OSSE with Typed Seeds

```python
type_counts = {
    'malware': 9999,
    'attack-pattern': 9999,
    'threat-actor': 9999,
    'vulnerability': 9999
}
communities, runtime, seeds = run_osse_with_typed_seeds(G_aggregated, type_counts)
```

### 3. Analyze & Visualize

```python
from your_module import analyze_community_edge_sources, visualize_community_graph_no_overlap

multi_source_communities, stats = analyze_community_edge_sources(communities, G_aggregated)

# Visualize a specific community
visualize_community_graph_no_overlap(
    G_aggregated,
    multi_source_communities[0],
    highlight={'used_seeds': seeds},
    enhance_info=True
)
```

