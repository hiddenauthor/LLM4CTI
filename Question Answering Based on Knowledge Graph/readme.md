# Question Answering Based on Knowledge Graph

This module evaluates whether knowledge graphs extracted by LLM4CTI can help large language models (LLMs) answer CTI-related multiple-choice questions more accurately.


## 🔄 Workflow

### 1. Input Preparation
- Load `RQ1 Evaluation.xlsx`, which includes:
  - CTI article text
  - Extracted knowledge graphs from different methods (LLM4CTI, CTIKG, GPT-4o)
  - Topic entity for each article
  - Ground truth article label (`article type`)

### 2. Question Generation
- Use LLM prompts to generate **10 entity-centric, multiple-choice questions per article**.
- Questions include 4 options (A/B/C/D) and are stored as JSON.

### 3. Prompt-Based Answering
- For each question, create a prompt:  
  > "The article or knowledge graph is provided below. Here is the question..."

### 4. Answer Evaluation
- Compare LLM answers with ground truth (A/B/C/D).
- Compute accuracy grouped by:
  - Method
  - Threat category (e.g., APT, Adware, Trojan)
---

### RQ2: Question Answering

We assess how well LLM4CTI’s knowledge graphs support downstream question answering by LLMs. The evaluation is based on multiple-choice questions generated from CTI articles.

We assess how well LLM4CTI’s knowledge graphs support downstream question answering by LLMs. The evaluation is based on multiple-choice questions generated from CTI articles.
| Approach                  | APT     | Adware  | Botnet  | Denial-of-Service attack | Ransomware | Spam/Phishing | Supply Chain Attack | Trojan horse | Virus/Worm | Vulnerability/exploit | Average   |
|---------------------------|---------|---------|---------|-------------------------|------------|---------------|---------------------|--------------|------------|----------------------|-----------|
| **LLM4CTI (Ours)**        | 98.33%  | 94.00%  | 87.50%  | 100.00%                 | 85.71%     | 90.00%        | 92.00%              | 90.00%       | 90.00%     | 86.00%               | 90.68%    |
| CTIKG                     | 88.33%  | 92.00%  | 90.00%  | 86.67%                  | 85.71%     | 86.67%        | 86.00%              | 66.67%       | 88.33%     | 88.00%               | 86.60%    |
| GPT-4o                    | 96.67%  | 90.00%  | 77.50%  | 93.33%                  | 82.86%     | 86.67%        | 94.00%              | 90.00%       | 85.00%     | 88.00%               | 87.96%    |
| GPT-4o (No Graph)         | 66.67%  | 86.00%  | 80.00%  | 66.67%                  | 81.43%     | 80.00%        | 76.00%              | 66.67%       | 78.33%     | 78.00%               | 77.28%    |

---
