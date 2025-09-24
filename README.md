# LLM4CTI

## Introduction

**LLM4CTI**  is a novel approach designed to extract security-relevant triples from unstructured Cyber Threat Intelligence (CTI) articles and organize them into structured knowledge graphs. With the rapid growth of CTI articles, manual analysis becomes impractical, and existing text-based methods struggle with long texts, while large language models (LLMs) face challenges such as hallucination and degraded performance with long inputs.

To address these limitations, LLM4CTI employs LLMs to process correlated sentences in CTI articles, reduces hallucination through chunk-wise knowledge extraction, and captures long-range dependencies by providing the entire article content as background information. Inspired by STIX, LLM4CTI defines a formal framework to model security entities and their interactions within a knowledge graph, enhancing threat knowledge extraction quality and supporting security applications such as relationship prediction and community detection.

## Overview

LLM4CTI's architecture comprises three main phases:

### 1. [Chunk-Level Knowledge Extraction](https://github.com/hiddenauthor/LLM4CTI/tree/main/Knowledge%20Graph%20Construction)
- **Step-wise Distributed Guidance:** Segments CTI articles into semantically coherent text chunks, guiding LLMs through structured entity extraction and relationship inference.
- **Dual-Context Multi-Chunk Extraction:** Combines local chunk context and global article content to extract both explicitly and implicitly stated relationships spanning distant chunks.

### 2. [Article-Level Knowledge Graph Construction](https://github.com/hiddenauthor/LLM4CTI/tree/main/Knowledge%20Graph%20Construction)
- Extracted entities and triples from all chunks are aggregated into an entity triple pool, followed by deduplication and entity resolution by a dedicated LLM agent, forming a unified article-level knowledge graph.

### 3. Knowledge Graph-Based Applications
- [**GNN-Based Relationship Prediction**](https://github.com/hiddenauthor/LLM4CTI/tree/main/GNN-Based%20Relationship%20Prediction): Trains a Graph Neural Network (GNN) on the constructed knowledge graph to predict relationships among security entities, supporting proactive defense strategies against emerging threats.


![LLM4CTI Overview](https://i.imgur.com/xtTcETK.png)  
Figure: The overall architecture of LLM4CTI, including chunk-level knowledge extraction, article-level graph construction, and graph-based security applications.

## Evaluation

We evaluate LLM4CTI on following curated datasets:
- **Diverse CTI Dataset**: 51 articles covering 8 threat categories.
- **Top Security Entity Dataset**: 302 articles focused on common threat entities.



### RQ1: Knowledge Graph Generation
We applied LLM4CTI to construct knowledge graphs from articles of the Diverse CTI Dataset. We used Gemini 2.5 Pro as an automated judge, validated against two human experts on 600 triples (agreement: 85.5% precision, 87.3% recall).  

**Results.** 
| Threat Category        | LLM4CTI(QWQ 32B) P / R / F1 | LLM4CTI(o4-mini) P / R / F1 | CTINexus P / R / F1 | GraphRAG P / R / F1 | Extractor P / R / F1 | CTIKG P / R / F1 |
|------------------------|---------------------------|---------------------------|---------------------|---------------------|----------------------|------------------|
| APT                    | 90.75 / 80.77 / 85.47    | 92.39 / 95.71 / **94.02** | 92.56 / 75.68 / 83.27 | **94.65** / 76.90 / 84.86 | 24.76 / 64.88 / 35.84 | 83.55 / 90.85 / 87.04 |
| Adware                 | 85.77 / **91.67** / 88.62 | 91.74 / 91.00 / **91.37** | 91.61 / 84.50 / 87.91 | **98.82** / 77.17 / 86.66 | 16.92 / 73.67 / 27.52 | 83.40 / 91.00 / 87.03 |
| Botnets                | 77.52 / 77.57 / 77.54    | 91.63 / **84.90** / **88.14** | 84.84 / 74.32 / 79.23 | **95.80** / 63.96 / 76.70 | 19.13 / 37.63 / 25.36 | 87.02 / 81.18 / 84.00 |
| Dos attack             | 83.76 / 78.57 / 81.08    | 87.41 / 52.38 / 65.51     | 88.80 / 52.38 / 65.89 | 82.96 / **95.24** / **88.68** | 6.94 / 19.05 / 10.18 | **95.00** / 33.33 / 49.35 |
| Ransomware             | 90.63 / 73.57 / 81.21    | 76.75 / 75.20 / 75.97     | **95.66** / 67.93 / 79.45 | 91.33 / 50.53 / 65.06 | 35.15 / 54.11 / 42.61 | 82.61 / **86.02** / **84.28** |
| Spam/Phishing          | 86.58 / 66.69 / 75.34    | 85.01 / 70.51 / **77.08** | 92.92 / 56.59 / 70.34 | **93.52** / 57.57 / 71.27 | 17.78 / 30.37 / 22.43 | 81.89 / **71.88** / 76.56 |
| Supply Chain Attacks   | 79.91 / **95.38** / 86.96 | 90.15 / 85.13 / 87.57     | 86.39 / 68.21 / 76.23 | **91.06** / 85.90 / **88.40** | 14.00 / 36.67 / 20.27 | 81.15 / 68.72 / 74.42 |
| Trojan                 | 85.05 / 63.18 / 72.50    | **98.46** / **85.89** / **91.75** | 73.32 / 45.84 / 56.41 | 97.92 / 58.58 / 73.31 | 14.13 / 37.87 / 20.58 | 79.17 / 64.53 / 71.10 |
| Virus/Worm             | 91.69 / 79.36 / 85.08    | 92.06 / 89.91 / **90.97** | **94.31** / 69.66 / 80.14 | 94.18 / 75.15 / 83.59 | 25.05 / 52.54 / 33.92 | 82.60 / **92.67** / 87.34 |
| Vuln. & exploits       | 84.17 / 79.84 / 81.95    | **93.07** / 85.19 / **88.96** | 86.10 / 64.58 / 73.80 | 89.87 / 61.49 / 73.02 | 23.19 / 52.73 / 32.21 | 90.33 / **87.14** / 88.71 |
| **Average (macro)**    | 85.58 / 78.66 / 81.58    | 89.87 / **81.58** / **85.13** | 88.65 / 65.97 / 75.27 | **93.01** / 70.25 / 79.16 | 19.70 / 45.95 / 27.09 | 84.67 / 76.73 / 78.98 |



---

### RQ2: Ablation Study

We study the effect of backend models, temperature, and chunk size on the performance of LLM4CTI.  

#### Impact of Different LLMs

| Model            | Precision | Recall | F1-Score |
|------------------|-----------|--------|----------|
| QWQ 32B          | 86.21%    | 80.29% | 83.15%   |
| OpenAI o4-mini   | 89.90%    | 80.98% | 85.21%   |
| GPT-4o           | 57.20%    | 44.29% | 49.92%   |
| LLaMa4 Maverick  | 67.30%    | 56.88% | 61.65%   |
| Qwen3 32B        | 78.59%    | 78.47% | 78.53%   |
| Qwen3 30B-A3B    | 79.94%    | 68.30% | 73.66%   |

#### Impact of Temperature (QWQ Model)

| Temperature | Precision | Recall | F1-Score |
|-------------|-----------|--------|----------|
| 0.2         | 86.21%    | 80.29% | 83.15%   |
| 0.6         | 68.53%    | 50.40% | 58.08%   |
| 1.0         | 62.81%    | 38.70% | 47.89%   |


#### Impact of Chunk Size (Qwen3 32B)

| Chunk Size | Precision | Recall | F1-Score |
|------------|-----------|--------|----------|
| 400        | 78.59%    | 78.47% | 78.53%   |
| 200        | 74.86%    | 83.68% | 79.02%   |
| 100        | 78.59%    | 86.38% | 82.30%   |


### RQ3: Semantic Relationship Prediction

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