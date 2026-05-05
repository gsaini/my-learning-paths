# 2. How RAG Works

**Retrieval-Augmented Generation (RAG)** is a technique that grounds an LLM's response in *external* knowledge it didn't see during training. Instead of relying only on the model's parametric memory, you fetch the most relevant documents at query time and stuff them into the prompt.

> RAG = Search + Generate. It is the dominant pattern for building LLM apps over private or fresh data without fine-tuning.

---

## Why RAG?

LLMs alone have three problems:

1. **Stale knowledge** — frozen at training cutoff.
2. **No private data** — never saw your wiki, your DB, your tickets.
3. **Hallucinations** — they confidently make up plausible-looking answers.

RAG fixes all three by retrieving authoritative source material before generation.

---

## The Two Phases

### Phase 1 — Indexing (offline)

```
Documents ─► Chunk ─► Embed ─► Store (Vector DB + metadata)
```

1. **Load** — pull from PDFs, websites, Confluence, SQL, S3, etc.
2. **Chunk** — split into 200–1000 token pieces (sentence, paragraph, semantic, recursive, or token-based splitting).
3. **Embed** — convert each chunk to a vector using a model like `text-embedding-3-large`, `voyage-3`, or `bge-large`.
4. **Store** — write vectors + original text + metadata into a vector DB (Pinecone, Qdrant, pgvector, Weaviate).

### Phase 2 — Querying (online)

```
User query ─► Embed ─► Vector search ─► Top-k chunks ─► Build prompt ─► LLM ─► Answer
```

1. **Embed** the query with the *same* embedding model.
2. **Retrieve** top-k similar chunks (k = 3–20 typically).
3. **(Optional) Rerank** with a cross-encoder for precision.
4. **Augment** the prompt: `"Use only the context below to answer..."`.
5. **Generate** — the LLM produces a grounded answer, often with citations.

---

## The Major RAG Variants

| Variant | What's different | When to use |
|---------|------------------|-------------|
| **Naive RAG** | Single retrieve → generate | Prototypes, simple Q&A |
| **Hybrid Search** | Vector + BM25/keyword union | When exact terms matter (codes, IDs) |
| **Reranking** | Re-score top-50 with cross-encoder, keep top-5 | Quality boost; cheap latency cost |
| **HyDE** | LLM hallucinates an answer, embeds *that*, retrieves on it | Sparse / abstract queries |
| **Multi-query** | LLM rewrites the question into N variations | Ambiguous user input |
| **GraphRAG** | Build a knowledge graph; traverse + retrieve | Multi-hop reasoning, entity-rich domains |
| **Agentic RAG** | An agent decides *whether* and *what* to retrieve | Complex tasks, mixed sources |
| **Contextual Retrieval** | Prepend a per-chunk summary before embedding | Anthropic's 2024 trick — big quality lift |

---

## Chunking — Where Most Quality Lives

Bad chunking ruins retrieval. Some heuristics:

- **Size:** 200–500 tokens typical; long chunks dilute, short chunks lose context.
- **Overlap:** 10–20% to avoid splitting key sentences.
- **Semantic chunking:** split on embedding-similarity boundaries instead of fixed length.
- **Structural chunking:** respect headings, lists, code blocks, tables.
- **Parent-child:** retrieve small precise chunks but pass the larger parent doc to the LLM.

---

## Evaluating a RAG System

Two axes:

**Retrieval quality**
- **Recall@k** — did we fetch the right chunks?
- **MRR / nDCG** — are good chunks ranked high?
- **Hit rate** — did *any* relevant chunk make it in?

**Generation quality**
- **Faithfulness** — does the answer stay grounded in the retrieved context?
- **Answer relevance** — does it actually address the question?
- **Context precision** — how much of the retrieved context was useful?

Tools: **RAGAS**, **TruLens**, **DeepEval**, **Phoenix/Arize**.

---

## Common Failure Modes

- **"Lost in the middle"** — the LLM ignores chunks in the middle of a long context.
- **Embedding model mismatch** between indexing and querying.
- **Chunk too large** → noisy; **too small** → missing context.
- **Overlapping but contradictory sources** → confused answer.
- **Stale index** — docs changed, vectors didn't.

---

## When NOT to use RAG

- The data fits in the context window → just include it (prompt caching makes this cheap).
- You need *reasoning over the entire corpus* (e.g., "summarize all docs") → use hierarchical summarization instead.
- The task needs *behavior change*, not knowledge → fine-tune.

---

## Further Reading

- Lewis et al., 2020 — original RAG paper
- Anthropic blog: *Contextual Retrieval*
- "RAG vs Long Context" — 2024 debates
