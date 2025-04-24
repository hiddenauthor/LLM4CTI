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
- [**Seed-Driven Community Detection**](https://github.com/hiddenauthor/LLM4CTI/tree/main/Seed-Driven%20Community%20Detection): Applies community detection algorithms using seed nodes categorized by threat types (e.g., malware, threat actors, vulnerabilities) to uncover densely connected subgraphs that represent different perspectives of the same threat across multiple CTI articles.
- [**Question Answering Based on Knowledge Graph**](https://github.com/hiddenauthor/LLM4CTI/tree/main/Question%20Answering%20based%20on%20Knowledge%20Graph): Leverages the structured knowledge graph to support LLMs in answering CTI-related multiple-choice questions, improving comprehension and decision-making on cyber threats.

![LLM4CTI Overview](https://i.imgur.com/Vmbwc7R.png)  
Figure: The overall architecture of LLM4CTI, including chunk-level knowledge extraction, article-level graph construction, and graph-based security applications.

## Evaluation

We evaluate LLM4CTI on three curated datasets:
- **Diverse CTI Dataset**: 51 articles covering 8 threat categories.
- **CTI Question Dataset**: 510 questions to assess knowledge comprehension.
- **Top Security Entity Dataset**: 302 articles focused on common threat entities.

### RQ1: Knowledge Graph Generation

We evaluate the effectiveness of LLM4CTI in transforming unstructured CTI articles into structured knowledge graphs. The evaluation is conducted using precision, recall, and F1-score against ground-truth graphs.

| Threat Category              | LLM4CTI Precision | LLM4CTI Recall | LLM4CTI F1 | CTIKG Precision | CTIKG Recall | CTIKG F1 | GPT-4o Precision | GPT-4o Recall | GPT-4o F1 | Extractor Precision | Extractor Recall | Extractor F1 |
|-----------------------------|-------------------|----------------|------------|------------------|--------------|----------|-------------------|----------------|-----------|-----------------------|-------------------|--------------|
| APT                         | 95.39%            | 86.18%         | 90.55%     | 99.58%           | 92.80%       | 96.07%   | 98.32%            | 89.15%         | 93.51%    | 62.21%                | 46.46%            | 53.19%       |
| Adware                      | 94.74%            | 83.64%         | 88.84%     | 93.09%           | 62.96%       | 75.12%   | 95.16%            | 73.58%         | 82.99%    | 70.79%                | 48.15%            | 57.31%       |
| Botnets                     | 93.80%            | 95.52%         | 94.65%     | 99.56%           | 91.04%       | 95.11%   | 100.00%           | 83.58%         | 91.06%    | 61.47%                | 54.55%            | 57.80%       |
| Spam & Phishing             | 91.76%            | 91.58%         | 91.67%     | 99.19%           | 86.59%       | 92.46%   | 99.12%            | 72.63%         | 83.83%    | 61.86%                | 50.51%            | 55.61%       |
| Supply Chain Attacks        | 91.89%            | 97.67%         | 94.69%     | 100.00%          | 96.30%       | 98.11%   | 96.43%            | 86.79%         | 91.36%    | 80.19%                | 34.04%            | 47.79%       |
| Trojan                      | 97.44%            | 88.76%         | 92.90%     | 96.29%           | 64.61%       | 77.33%   | 93.68%            | 68.75%         | 79.30%    | 52.03%                | 43.64%            | 47.47%       |
| Virus                       | 97.58%            | 90.15%         | 93.72%     | 97.72%           | 80.45%       | 88.25%   | 97.98%            | 69.12%         | 81.06%    | 67.20%                | 53.68%            | 59.68%       |
| Vulnerabilities & Exploits  | 99.28%            | 95.35%         | 97.27%     | 94.03%           | 88.64%       | 91.25%   | 100.00%           | 87.80%         | 93.51%    | 53.12%                | 71.74%            | 61.04%       |
| **Average**                 | **95.23%**        | **91.11%**     | **93.04%** | **97.43%**       | 82.92%       | 89.21%   | 97.59%            | 78.93%         | 87.08%    | 63.61%                | 50.34%            | 54.99%       |

---

### RQ2: Question Answering

We assess how well LLM4CTI’s knowledge graphs support downstream question answering by LLMs. The evaluation is based on multiple-choice questions generated from CTI articles.

| Approach               | APT     | Adware  | Botnets | Spam & Phishing | Supply Chain Attacks | Trojan  | Virus   | Vulns & Exploits | **Average** |
|------------------------|---------|---------|---------|------------------|------------------------|---------|---------|-------------------|-------------|
| **LLM4CTI (Ours)**     | 98.33%  | 94.00%  | 86.00%  | 88.75%           | 92.00%                 | 86.00%  | 95.00%  | 86.00%            | **90.40%**  |
| CTIKG                  | 88.33%  | 92.00%  | 88.00%  | 86.25%           | 86.00%                 | 79.00%  | 91.67%  | 88.00%            | 86.60%      |
| GPT-4o                 | 96.67%  | 90.00%  | 78.00%  | 85.00%           | 94.00%                 | 85.00%  | 88.33%  | 88.00%            | 87.80%      |
| GPT-4o (No Graph)      | 66.67%  | 86.00%  | 80.00%  | 80.00%           | 76.00%                 | 75.00%  | 81.67%  | 78.00%            | 77.60%      |

---

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

---

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
