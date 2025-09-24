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
The input is a `.pkl` file containing the output from `Knowledge Graph Construction`.

### 2. Train and Evaluate
Run through the notebook. It will:
- Build and train the model
- Output the evaluation metrics 


### RQ3: Relationship Prediction

We evaluate how LLM4CTI’s knowledge graphs can support link prediction using GNN models. This task measures the ability to uncover latent relationships among security entities.

#### Results of Link Prediction

| Entity Pair Type                     | Hit@1  | Hit@5  | Hit@10 |
|--------------------------------------|--------|--------|--------|
| malware → malware                    | 9.14%  | 64.00% | 80.57% |
| malware → threat actor/intrusion     | 3.36%  | 57.98% | 78.15% |
| infrastructure → malware             | 6.90%  | 49.43% | 63.22% |
| general software → malware           | 5.33%  | 34.67% | 45.33% |
| attack pattern → malware             | 2.78%  | 54.17% | 62.50% |
| infrastructure → threat actor        | 11.11% | 50.79% | 71.43% |
| attack pattern → threat actor        | 3.57%  | 42.86% | 71.43% |
| threat actor → threat actor          | 2.33%  | 60.47% | 74.42% |
| attack pattern → infrastructure      | 3.33%  | 53.33% | 60.00% |
| attack pattern → attack pattern      | 7.41%  | 74.07% | 81.48% |
