# Knowledge Graph Construction

This module implements the article-level knowledge graph construction phase of the LLM4CTI framework, transforming extracted triples from chunked CTI articles into a unified and structured knowledge graph.

## 🔄 Workflow

The `Knowledge Graph Construction.ipynb` notebook implements the following pipeline to construct article-level knowledge graphs from raw CTI articles:

1. **Input Preparation**
   - Load the full text of a CTI article.
   - Automatically split the article into overlapping semantic chunks using `RecursiveCharacterTextSplitter` (default: 400 tokens per chunk, 10% overlap).

2. **Chunk-Level Extraction**
   - For each chunk, create a structured prompt using `chunk_prompt_maker(full_text, chunk_text)`.
   - Use `ask_group_link()` to query the LLM in parallel and extract entities and relationships for each chunk.

3. **Chunk Response Processing**
   - Clean and format the raw LLM responses.
   - Wrap each chunk result with `[Chunk_i_START]` and `[Chunk_i_END]` for merging.

4. **Chunk-Level Merging**
   - Concatenate all chunk responses into a single merged block.
   - Use `merger_prompt_maker(merged_chunk_text)` to construct a final prompt for merging all chunk results.

5. **Article-Level Graph Construction**
   - Use another LLM query to merge entities, deduplicate triples, and add inferred links.
   - The final graph is output in JSON format with clearly defined entity and relationship sections.

---

## 🚀 How to Use

You can use this notebook to process a single CTI article and obtain a structured knowledge graph. Here's a minimal example of how to use the functions:

```python
# Load the full article content
with open('example_article.txt', 'r') as f:
    article_text = f.read()

# Generate the merged knowledge graph
graph_json = process_article(article_text, mode='o', server='local')

# Save the result
with open('result/final_graph.json', 'w') as f:
    f.write(graph_json)
```

### Parameters
- `mode='o'` sets the notebook to use optimized parallel chunk processing.
- `server='local'` assumes a local LLM is used. You may replace this with `gpt4o` if using OpenAI APIs.


## Evaluation
We evaluate the effectiveness of LLM4CTI in transforming unstructured CTI articles into structured knowledge graphs. 

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


