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


