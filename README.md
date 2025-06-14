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

| Threat Category              | \tool Precision | \tool Recall | \tool F1 | CTIKG Precision | CTIKG Recall | CTIKG F1 | GPT-4o Precision | GPT-4o Recall | GPT-4o F1 | Extractor Precision | Extractor Recall | Extractor F1 |
|------------------------------|-----------------|--------------|----------|------------------|--------------|----------|-------------------|---------------|-----------|---------------------|------------------|--------------|
| APT                          | 95.39%          | 86.18%       | 90.55%   | 99.58%           | 92.80%       | 96.07%   | 98.32%            | 89.15%        | 93.51%    | 62.21%              | 46.46%           | 53.19%       |
| Adware                       | 94.74%          | 83.64%       | 88.84%   | 93.09%           | 62.96%       | 75.12%   | 95.16%            | 73.58%        | 82.99%    | 70.79%              | 48.15%           | 57.31%       |
| Botnet                       | 94.49%          | 96.83%       | 95.64%   | 99.50%           | 94.83%       | 97.11%   | 97.83%            | 82.54%        | 89.54%    | 61.90%              | 54.39%           | 57.90%       |
| Denial-of-Service Attack     | 95.24%          | 89.47%       | 92.27%   | 98.73%           | 73.68%       | 84.39%   | 96.77%            | 89.47%        | 92.98%    | 42.62%              | 61.54%           | 50.36%       |
| Ransomware                   | 98.57%          | 96.27%       | 97.41%   | 98.37%           | 68.18%       | 80.54%   | 98.59%            | 80.00%        | 88.33%    | 54.23%              | 46.28%           | 49.94%       |
| Spam/Phishing                | 92.17%          | 91.84%       | 92.00%   | 98.61%           | 66.03%       | 79.09%   | 99.18%            | 73.47%        | 84.41%    | 59.59%              | 49.02%           | 53.79%       |
| Supply Chain Attack          | 91.89%          | 98.28%       | 94.98%   | 100.00%          | 96.30%       | 98.11%   | 96.43%            | 86.79%        | 91.36%    | 80.19%              | 34.04%           | 47.79%       |
| Trojan horse                 | 94.12%          | 73.13%       | 82.31%   | 90.00%           | 50.00%       | 64.29%   | 76.19%            | 46.27%        | 57.57%    | 59.74%              | 35.82%           | 44.79%       |
| Virus/Worm                   | 97.48%          | 90.43%       | 93.82%   | 98.50%           | 88.24%       | 93.09%   | 99.02%            | 71.43%        | 82.99%    | 59.66%              | 58.82%           | 59.24%       |
| Vulnerability/exploit        | 99.28%          | 95.35%       | 97.27%   | 94.03%           | 88.64%       | 91.25%   | 100.00%           | 87.80%        | 93.51%    | 53.12%              | 71.74%           | 61.04%       |
| **Average**                  | 95.34%          | 90.14%       | 92.51%   | 97.04%           | 78.17%       | 85.91%   | 95.75%            | 78.05%        | 85.72%    | 60.41%              | 50.63%           | 53.54%       |


---

### RQ2: Question Answering

We assess how well LLM4CTI’s knowledge graphs support downstream question answering by LLMs. The evaluation is based on multiple-choice questions generated from CTI articles.
| Approach                  | APT     | Adware  | Botnet  | Denial-of-Service attack | Ransomware | Spam/Phishing | Supply Chain Attack | Trojan horse | Virus/Worm | Vulnerability/exploit | Average   |
|---------------------------|---------|---------|---------|-------------------------|------------|---------------|---------------------|--------------|------------|----------------------|-----------|
| **LLM4CTI (Ours)**        | 98.33%  | 94.00%  | 87.50%  | 100.00%                 | 85.71%     | 90.00%        | 92.00%              | 90.00%       | 90.00%     | 86.00%               | 90.68%    |
| CTIKG                     | 88.33%  | 92.00%  | 90.00%  | 86.67%                  | 85.71%     | 86.67%        | 86.00%              | 66.67%       | 88.33%     | 88.00%               | 86.60%    |
| GPT-4o                    | 96.67%  | 90.00%  | 77.50%  | 93.33%                  | 82.86%     | 86.67%        | 94.00%              | 90.00%       | 85.00%     | 88.00%               | 87.96%    |
| GPT-4o (No Graph)         | 66.67%  | 86.00%  | 80.00%  | 66.67%                  | 81.43%     | 80.00%        | 76.00%              | 66.67%       | 78.33%     | 78.00%               | 77.28%    |

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
