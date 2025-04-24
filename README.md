# LLM4CTI

## Introduction

**LLM4CTI**  is a novel approach designed to extract security-relevant triples from unstructured Cyber Threat Intelligence (CTI) articles and organize them into structured knowledge graphs. With the rapid growth of CTI articles, manual analysis becomes impractical, and existing text-based methods struggle with long texts, while large language models (LLMs) face challenges such as hallucination and degraded performance with long inputs.

To address these limitations, LLM4CTI employs LLMs to process correlated sentences in CTI articles, reduces hallucination through chunk-wise knowledge extraction, and captures long-range dependencies by providing the entire article content as background information. Inspired by STIX, LLM4CTI defines a formal framework to model security entities and their interactions within a knowledge graph, enhancing threat knowledge extraction quality and supporting security applications such as relationship prediction and community detection.

## Overview

LLM4CTI's architecture comprises three main phases:

### 1. Chunk-Level Knowledge Extraction
- **Step-wise Distributed Guidance:** Segments CTI articles into semantically coherent text chunks, guiding LLMs through structured entity extraction and relationship inference.
- **Dual-Context Multi-Chunk Extraction:** Combines local chunk context and global article content to extract both explicitly and implicitly stated relationships spanning distant chunks.

### 2. Article-Level Knowledge Graph Construction
- Extracted entities and triples from all chunks are aggregated into an entity triple pool, followed by deduplication and entity resolution by a dedicated LLM agent, forming a unified article-level knowledge graph.

### 3. Knowledge Graph-Based Applications
- **GNN-Based Relationship Prediction:** Trains a Graph Neural Network (GNN) on the constructed knowledge graph to predict relationships among security entities, supporting proactive defense strategies against emerging threats.
- **Seed-Driven Community Detection:** Applies community detection algorithms using seed nodes categorized by threat types (e.g., malware, threat actors, vulnerabilities) to uncover densely connected subgraphs that represent different perspectives of the same threat across multiple CTI articles.

![LLM4CTI Overview](https://i.imgur.com/Vmbwc7R.png)  
Figure: The overall architecture of LLM4CTI, including chunk-level knowledge extraction, article-level graph construction, and graph-based security applications.
