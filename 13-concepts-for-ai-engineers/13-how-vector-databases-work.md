# 13. How Vector Databases Work

A **vector database** stores high-dimensional embedding vectors and answers **"find me the most similar items"** queries efficiently. They're the storage layer behind most RAG systems and a key building block of agent memory and semantic search.

> If a relational DB is built around exact match (`WHERE id = 42`), a vector DB is built around **fuzzy match** (`closest(embedding, my_query)`).

---

## What Is an Embedding?

An **embedding** is a fixed-length vector (e.g., 1024 floats) that represents the *meaning* of an item — a sentence, an image, a user, a product. Two items with similar meaning have vectors close together in the embedding space.

```
"how do I reset my password?" → [0.12, -0.04, 0.88, ..., 0.31]   (1536 dims)
"forgot my login info"        → [0.10, -0.06, 0.85, ..., 0.29]   (close vector)
"what is the weather today?"  → [-0.42, 0.91, 0.05, ..., -0.77]  (far vector)
```

Embeddings are produced by **embedding models**: `text-embedding-3-large`, `voyage-3`, `bge-large`, `all-MiniLM`, etc.

---

## What a Vector DB Does

Given a query vector `q`, find the **k nearest neighbors** (k-NN) in a corpus of millions or billions of vectors — fast.

- Brute force: compute distance to every vector. O(N·d). Fine up to ~100k vectors.
- At scale, use **Approximate Nearest Neighbor (ANN)** indexes — sub-linear time, slight recall trade-off.

---

## Distance Metrics

| Metric | Formula | Use when |
|--------|---------|----------|
| **Cosine** | `1 − (a·b) / (‖a‖‖b‖)` | Most embeddings (orientation matters) |
| **Dot product** | `a·b` | When embeddings are normalized; cheaper |
| **Euclidean (L2)** | `‖a − b‖` | Geometric distance; some image embeddings |

Most text embeddings are designed for **cosine** similarity.

---

## ANN Index Types

### HNSW — Hierarchical Navigable Small World
- Multi-layer graph; greedy navigation from coarse to fine.
- **Default for most modern vector DBs.**
- Great recall and speed; high memory cost.

### IVF — Inverted File Index
- Cluster vectors into N partitions; search only the most likely ones.
- Lower memory; tunable speed/recall trade-off.
- Often combined with PQ.

### PQ — Product Quantization
- Compress each vector into a few bytes.
- Massive memory savings; small recall hit.
- Often paired with IVF: **IVF-PQ**.

### Flat
- No index — brute force.
- Use for small corpora (<100k) or as a recall baseline.

### DiskANN / Vamana
- Disk-resident graph indexes; scale to billions on commodity hardware.

---

## Capabilities Beyond Pure k-NN

Modern vector DBs offer more than nearest-neighbor:

- **Metadata filtering** — `WHERE category = "support" AND lang = "en"`.
- **Hybrid search** — combine vector similarity with BM25/keyword.
- **Sparse + dense** — separate sparse (keyword) and dense (semantic) indexes; fuse results.
- **Multi-tenant namespaces** — isolation between customers.
- **Time decay** — boost recent items.
- **Re-ranking** — second-stage cross-encoder over top candidates.

---

## A Typical Workflow

```
Index time:
  text → embedding model → vector + metadata → DB.upsert()

Query time:
  query → embedding model → DB.query(top_k=10, filter={...}) → results
  → rerank → return top-3 to the LLM
```

---

## Tuning Knobs

- **k** (neighbors returned) — usually 5–20 for RAG.
- **HNSW `M` and `efConstruction` / `efSearch`** — recall vs speed.
- **IVF `nlist` and `nprobe`** — partitions and probe count.
- **Filter strategy** — pre-filter (apply filter then search) vs post-filter (search then filter).

---

## Vector DB Landscape

| System | Style | Notable for |
|--------|-------|-------------|
| **Pinecone** | Hosted, serverless | Managed, simple API |
| **Weaviate** | OSS + cloud | GraphQL, modules, hybrid search |
| **Qdrant** | OSS + cloud | Rust core, good filtering |
| **Milvus / Zilliz** | OSS + cloud | Scale to billions |
| **pgvector** | Postgres extension | Reuse your DB; great default |
| **Chroma** | Lightweight, Python-first | Local dev, prototypes |
| **Elasticsearch / OpenSearch** | Search + vectors | Already-on-stack option |
| **Vespa** | Search engine | Big-tech-grade hybrid |
| **Redis** | Vector module | If you already run Redis |

---

## Beyond RAG — What Else?

- **Semantic search** — site search, doc search, code search.
- **Recommendations** — "more like this."
- **Deduplication** — find near-duplicates.
- **Clustering & exploration** — visualize what's in a corpus.
- **Agent memory** — long-term episodic recall.
- **Anomaly detection** — outliers in embedding space.

---

## Common Pitfalls

- **Embedding model mismatch** between indexing and query time → garbage results.
- **Stale index** — corpus changed, vectors didn't.
- **Bad chunking upstream** → retrieval can't recover.
- **Ignoring metadata** — filtering would have solved a "bad recall" problem.
- **Over-rebuilding** — full re-index every change is expensive; use upserts.
- **Cosine on un-normalized vectors** — bugs that look like quality issues.

---

## When You Don't Need a Vector DB

- Corpus fits in memory and is small (<100k items) → brute force is fine.
- Pure keyword needs → BM25 / Elasticsearch may be enough.
- Already running Postgres at modest scale → `pgvector` covers you.

Don't add a new system because it's trendy.

---

## Further Reading

- Pinecone "Faiss tutorial" series
- Weaviate / Qdrant docs (excellent introductions)
- *Faiss* paper (Johnson, Douze, Jégou)
- HNSW paper (Malkov & Yashunin, 2016)
