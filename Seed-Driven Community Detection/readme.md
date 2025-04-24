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
df = pd.read_excel("RQ3.xlsx")
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
multi_source_communities, stats = analyze_community_edge_sources(communities, G_aggregated)

# Visualize a specific community
visualize_community_graph_no_overlap(
    G_aggregated,
    multi_source_communities[0],
    highlight={'used_seeds': seeds},
    enhance_info=True
)
```


### RQ4: Community Detection

We examine whether LLM4CTI can uncover cross-document semantic clusters through community detection. Communities are evaluated for their edge coverage, article support, and relevance.

| Central Node        | Threat Type   | Edges From Core | Edges From Related | Related Article Count | Unrelated Edges |
|---------------------|---------------|------------------|---------------------|------------------------|------------------|
| DDoS                | attack-pattern| 10               | 21                  | 10                     | 3                |
| Ransomware Attack   | attack-pattern| 8                | 15                  | 3                      | 0                |
| Malspam             | attack-pattern| 9                | 11                  | 4                      | 0                |
| Ursnif              | malware       | 24               | 8                   | 3                      | 2                |
| Scarab              | malware       | 26               | 2                   | 2                      | 0                |
| ZeuS Trojan         | malware       | 6                | 20                  | 12                     | 1                |
| Locky ransomware    | malware       | 10               | 12                  | 3                      | 1                |
| CryptoWall 4.0      | malware       | 15               | 2                   | 1                      | 1                |
| WannaMine           | malware       | 13               | 3                   | 1                      | 1                |
| Kelihos.B           | malware       | 19               | 3                   | 2                      | 0                |
| Neutrino            | malware       | 5                | 16                  | 12                     | 1                |
| Koobface gang       | threat-actor  | 21               | 9                   | 5                      | 0                |
| Lazarus             | threat-actor  | 6                | 4                   | 2                      | 0                |
| ProjectM            | threat-actor  | 13               | 2                   | 2                      | 0                |
| Paunch              | threat-actor  | 19               | 24                  | 13                     | 0                |
| Java vulnerability  | vulnerability | 10               | 13                  | 6                      | 0                |
| SMB vulnerability   | vulnerability | 8                | 38                  | 14                     | 0                |
| **Total**           | –             | 222              | 203                 | 95                     | 10               |

