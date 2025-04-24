# GNN-Based Relationship Prediction

This module implements the **GNN-based link prediction** component of the LLM4CTI framework. It takes the knowledge graphs constructed from CTI articles and applies a Graph Neural Network (GNN) model to uncover unseen or emerging relationships between threat entities (e.g., malware, vulnerabilities, threat actors).

---

## 🔄 Workflow

The `GNN-Based Relationship Prediction.ipynb` includes the following pipeline:

1. **Knowledge Graph Parsing**
   - Use `build_knowledge_graph()` to convert per-article outputs into a structured `networkx.MultiDiGraph`.
   - Use `aggregate_knowledge_graph()` to merge all article graphs into a global graph.

2. **Node Normalization**
   - Apply lemmatization (via spaCy) to merge semantically equivalent node names.
   - Select the most connected node as the canonical representative.

3. **Train/Test Split**
   - Randomly split nodes into 70% training and 30% test partitions.
   - Build induced subgraphs to isolate link prediction tasks.

4. **Embedding Generation**
   - Generate node embeddings using external models (e.g., OpenAI or local tools).
   - Cache embeddings for reusability.

5. **Structural Feature Computation**
   - Compute 10+ structural features (shortest path, Jaccard, Adamic-Adar, etc.) for each edge using `networkx`.

6. **Graph Construction**
   - Convert graphs to PyTorch Geometric format using `from_networkx()`.

7. **Model Definition**
   - Use a hybrid GNN architecture combining GCN, GAT, and GraphSAGE.
   - Fuse node embeddings and structural features for edge classification.

8. **Training & Evaluation**
   - Use hard negative sampling for robust training.
   - Evaluate using AUC, accuracy, and a 5-fold type-aware breakdown.

9. **Result Visualization**
   - Output ROC curves and generate type-pair statistics on predicted links.

---

## 🚀 How to Use

### 1. Prepare Input Data
The input is a `.xlsx` file containing the output from `Knowledge Graph Construction`.

### 2. Train and Evaluate
Run through the notebook. It will:
- Build and train the model
- Output top-k correctly predicted links
- Save a ROC curve at `roc_curve.png`


### RQ3: Relationship Prediction

We evaluate how LLM4CTI’s knowledge graphs can support link prediction using GNN models. This task measures the ability to uncover latent relationships among security entities.

| Link Type                         | Count | Precision | Recall  | F1 Score |
|----------------------------------|-------|-----------|---------|----------|
| malware–malware                 | 225   | 100.00%   | 87.80%  | 93.51%   |
| attack pattern–general entity   | 225   | 100.00%   | 88.97%  | 94.16%   |
| campaign–domain name            | 215   | 100.00%   | 81.11%  | 89.57%   |
| domain name–malware             | 195   | 100.00%   | 85.19%  | 92.00%   |
| domain name–threat actor        | 190   | 100.00%   | 90.00%  | 94.74%   |
| malware–threat actor            | 185   | 100.00%   | 86.88%  | 92.98%   |
| general entity–malware          | 185   | 100.00%   | 80.00%  | 88.89%   |
| identity–malware                | 175   | 100.00%   | 88.00%  | 93.62%   |
| campaign–threat actor           | 170   | 100.00%   | 82.76%  | 90.57%   |
| infrastructure–malware          | 170   | 100.00%   | 88.28%  | 93.77%   |
| hacker tool–malware             | 165   | 100.00%   | 85.33%  | 92.09%   |
| file–malware                    | 160   | 100.00%   | 74.12%  | 85.14%   |
| campaign–identity               | 160   | 100.00%   | 87.69%  | 93.44%   |
| identity–threat actor           | 155   | 97.96%    | 83.48%  | 90.14%   |
| infrastructure–threat actor     | 140   | 100.00%   | 84.21%  | 91.43%   |
| **Average**                     | –     | **99.86%**| 84.92%  | **91.74%**|
