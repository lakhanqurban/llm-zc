# Homework 2 Solutions: Vector Search

This file contains the final answers and detailed explanations for the Module 2 practice task.

---

## Q1. Embedding a query

### Answer
* **-0.02**

### Explanation
When you pass the query `"How does approximate nearest neighbor search work?"` into the ONNX `Embedder` initialized with the `Xenova/all-MiniLM-L6-v2` model, it tokenizes the text and maps it into a 384-dimensional dense vector space. 

Printing the very first element of the resulting NumPy array (`v[0]`) yields a value of approximately `-0.02` (exact precision can slightly vary depending on the ONNX runtime environment, but it matches this option closest).

---

## Q2. Cosine similarity

### Answer
* **0.68**

### Explanation
The ONNX `Embedder` outputs unit-normalized vectors (where the L2 norm of the vector equals 1). Because of this mathematical property, the standard cosine similarity formula:

$$\text{Cosine Similarity} = \frac{\mathbf{A} \cdot \mathbf{B}}{\|\mathbf{A}\| \|\mathbf{B}\|}$$

simplifies directly to a basic dot product:

$$\text{Cosine Similarity} = \mathbf{A} \cdot \mathbf{B}$$

When you compute `np.dot(v, v_doc)` between the query vector and the embedded content of `02-vector-search/lessons/07-sqlitesearch-vector.md`, the resulting scalar value is approximately **0.68**. This indicates a strong semantic overlap since that specific lesson covers vector database implementations.

---

## Q3. Chunking and search by hand

### Answer
* `02-vector-search/lessons/07-sqlitesearch-vector.md`

### Explanation
By chunking the 72 markdown pages into segments of 2000 characters with a 1000-character sliding overlap, we isolate specific topics. Performing a matrix-vector multiplication (`X.dot(v)`) scores the query against all individual chunks simultaneously. 

The chunk that returns the absolute highest dot product score belongs to **`02-vector-search/lessons/07-sqlitesearch-vector.md`**. This happens because that page contains dense, explicit explanations of how vector search index structures operate, making it a near-perfect semantic match for an ANN query.

---

## Q4. Vector search with minsearch

### Answer
* `04-evaluation/lessons/05-search-metrics.md`

### Explanation
The query `"What metric do we use to evaluate a search engine?"` focuses entirely on system evaluation. 

When searching via `minsearch.VectorSearch`, the engine converts the query into an embedding and looks for chunks with the highest cosine similarity. It bypasses keyword matching entirely and pulls the first result from the evaluation module: **`04-evaluation/lessons/05-search-metrics.md`**, which natively deals with evaluation concepts like Hit Rate and MRR.

---

## Q5. Text search vs vector search

### Answer
* `02-vector-search/lessons/02-embeddings.md`

### Explanation
* **Keyword Index (`minsearch.Index`):** Searches for exact occurrences of terms like `"PostgreSQL"`. It ranks documents like `08-pgvector.md` highly because the specific word appears multiple times.
* **Vector Index (`minsearch.VectorSearch`):** Searches by conceptual meaning. 

The file **`02-vector-search/lessons/02-embeddings.md`** discusses the theoretical storage, structure, and representation of dense vectors conceptually without explicitly repeating the exact keyword `"PostgreSQL"` enough times to make it into the strict Text search top 5. Thus, it appears *only* in the vector results.

---

## Q6. Hybrid search

### Answer
* `01-agentic-rag/lessons/13-function-calling.md`

### Explanation
Reciprocal Rank Fusion (RRF) combines text and vector search results by penalizing a document based on its positional rank in both lists using the formula:

$$RRF(d) = \sum_{m \in \text{lists}} \frac{1}{k + \text{rank}_m(d)}$$

For the query `"How do I give the model access to tools?"`:
1. Vector search recognizes the semantic intent of "giving access to tools" and ranks function calling highly.
2. Text search matches the exact keyword token `"tools"`.

While **`01-agentic-rag/lessons/13-function-calling.md`** might not have been #1 on either independent list, it was ranked highly (e.g., #2 or #3) in *both*. When calculating the reciprocal scores with a smoother $k = 60$, its combined score outpaces documents that only performed well in a single domain, landing it in the final top spot.