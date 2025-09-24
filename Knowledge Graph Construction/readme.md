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
# Load the content of each article
df = pd.read_excel('Articles.xlsx')
Articlecontent=df['content'].tolist()

# Generate the knowledge graph of each articles
KnowledgeGraph={}
kg_text=process_texts_to_kg_strings_in_batch(Articlecontent, temp=0.1, token=4*1024, model='gpt-4o', api_key='sk-xxxx', api_base="https://api.openai.com/v1")

# Merger all article graphs into one knowledge graph
df['KnowledgeGraph']=kg_text
G_aggregated = aggregate_knowledge_graph(df, 'KnowledgeGraph', 'ArticleIndex')
```

## Evaluation

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



