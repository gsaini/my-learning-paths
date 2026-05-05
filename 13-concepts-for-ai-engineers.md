# 13 Concepts for AI Engineers

> Source: *System Design Playbook* by Neo Kim — newsletter.systemdesign.one

---

## 1. How MCP Works
**Model Context Protocol (MCP)** is an open standard (introduced by Anthropic) that lets LLMs connect to external tools, data sources, and systems through a uniform interface.
- **Architecture:** Host (LLM app) ↔ MCP Client ↔ MCP Server ↔ Resource (DB, API, file system).
- **Primitives:** Tools (functions the model can call), Resources (read-only data), Prompts (reusable templates).
- **Why it matters:** Replaces brittle, one-off integrations with a plug-and-play standard — the "USB-C for AI."

## 2. How RAG Works
**Retrieval-Augmented Generation** grounds an LLM's response in external knowledge.
- **Pipeline:** Ingest → Chunk → Embed → Store in vector DB → Retrieve top-k on query → Augment prompt → Generate.
- **Variants:** Naive RAG, Hybrid (vector + keyword/BM25), Re-ranking, GraphRAG, Agentic RAG.
- **Why:** Reduces hallucinations, enables up-to-date and private-data answers without retraining.

## 3. How AI Agents Work
An **AI agent** is an LLM with a loop: **Perceive → Reason → Act → Observe → Repeat**.
- **Components:** LLM brain, tools, memory (short/long-term), planner, executor.
- **Loop styles:** ReAct (Reason+Act), Plan-and-Execute, Reflexion.
- Agents differ from chatbots because they take *actions* in the world (call APIs, write files, browse).

## 4. AI Coding Workflow 101
The end-to-end loop for using AI to write production code.
- **Steps:** Spec/PRD → Context gathering → Plan → Code generation → Test → Review → Iterate.
- **Best practices:** Small, scoped tasks; clear acceptance criteria; human-in-the-loop review; commit checkpoints.
- **Tools:** Claude Code, Cursor, Copilot, Aider — each with different agent autonomy levels.

## 5. LLM Evals — Core Concepts
How you measure if an LLM/agent is actually good.
- **Types:** Reference-based (BLEU, ROUGE, exact match), reference-free (LLM-as-judge), human eval, task-specific (e.g., HumanEval for code).
- **Dimensions:** Accuracy, faithfulness, helpfulness, safety, latency, cost.
- **Practice:** Build a golden eval set, run regressions on every prompt/model change, track via dashboards.

## 6. What is Context Engineering?
The discipline of curating *everything* the model sees in its context window — supersedes "prompt engineering."
- **Includes:** System prompt, instructions, retrieved docs, tool definitions, memory, examples, output schema.
- **Techniques:** Compression, summarization, chunk ordering, prompt caching, context-window budgeting.
- **Goal:** Maximize signal, minimize noise, stay within token limits and cost budgets.

## 7. Multi-Agent Architectures
Patterns where multiple specialized agents collaborate.
- **Orchestrator–worker:** A lead agent delegates subtasks (Anthropic's research agent uses this).
- **Hierarchical / Supervisor:** Tree of agents with a manager.
- **Network / Swarm:** Peer agents handing off (LangGraph, CrewAI).
- **Trade-offs:** Better task decomposition vs. higher cost, latency, and coordination complexity.

## 8. How to Design an AI Agent
A practical design checklist:
1. **Define the job** — single, measurable outcome.
2. **Pick the model** — capability vs. cost vs. latency.
3. **Tools** — minimal, well-described, idempotent where possible.
4. **Memory** — short-term (scratchpad), long-term (vector store).
5. **Control loop** — ReAct, plan-and-execute, etc.
6. **Guardrails** — input validation, output schemas, rate limits, human approval for risky actions.
7. **Eval & observability** — traces, metrics, replays.

## 9. LLM Concepts — A Deep Dive
Foundational ideas every AI engineer should know:
- **Tokens & tokenization** (BPE).
- **Transformer architecture:** attention, embeddings, positional encoding.
- **Training stages:** pretraining → SFT → RLHF/DPO.
- **Inference knobs:** temperature, top-p, top-k, max tokens.
- **Capabilities:** in-context learning, chain-of-thought, function calling, structured outputs.

## 10. Agentic Patterns, Simply Explained
Common reusable design patterns:
- **ReAct** — interleave reasoning and tool calls.
- **Reflection** — agent critiques and revises its own output.
- **Tool use** — function calling.
- **Planning** — produce a plan, then execute.
- **Multi-agent** — specialized roles (researcher, coder, reviewer).
- **Routing** — classifier sends task to the right specialist.

## 11. What is Reinforcement Learning?
A learning paradigm where an agent learns by trial and error to maximize a reward.
- **Pieces:** Agent, Environment, State, Action, Reward, Policy.
- **Algorithms:** Q-learning, Policy Gradient, PPO, DPO.
- **In LLMs:** RLHF aligns models to human preferences; RLAIF uses AI feedback; recent reasoning models use RL on verifiable rewards (math, code).

## 12. How AI Chat Assistants Work
Anatomy of a ChatGPT/Claude-style product:
- **Frontend** chat UI ↔ **API gateway** ↔ **Orchestrator** (system prompt, history, tools, RAG) ↔ **LLM**.
- **Streaming** tokens via SSE/WebSockets for fast UX.
- **Stateful conversation:** message history with truncation/summarization; persistent memory.
- **Add-ons:** moderation/safety, function calling, file uploads, multimodal input.

## 13. How Vector Databases Work
Specialized DBs for similarity search over high-dimensional embeddings.
- **Index types:** HNSW (graph), IVF (cluster), PQ (compression), Flat (brute force).
- **Distance metrics:** cosine, dot product, L2.
- **Capabilities:** ANN search, metadata filtering, hybrid (vector + keyword) search.
- **Examples:** Pinecone, Weaviate, Qdrant, Milvus, pgvector, Chroma.
- Powers the "Retrieve" step in RAG and long-term agent memory.
