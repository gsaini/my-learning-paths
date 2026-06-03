# AI Engineer Learning Path 2026

A focused, 3-month roadmap to move from building agent demos to shipping
production-grade AI systems. The emphasis is deliberately on the layers that
turn prototypes into reliable, observable, and optimized services —
evaluation, serving, retrieval quality, post-training, and safety.

## How to Use This Path

- Each section lists concrete tools to learn and a short reason they matter.
- The monthly plan pairs learning with a shippable outcome so the work lands
  in a real repository, not just notes.
- Check items off as you complete them.

## Focus Areas

### 1. Evaluation and Observability

The single highest-leverage gap. This is what distinguishes a senior AI
engineer: the ability to measure, trace, and debug LLM systems.

- [ ] **Langfuse** or **LangSmith** — tracing, cost and latency, prompt versioning
- [ ] **Ragas** — RAG evaluation (faithfulness, context precision and recall)
- [ ] **DeepEval** or **Promptfoo** — assertion-based and regression testing for LLMs
- [ ] **OpenTelemetry GenAI** semantic conventions for instrumentation

### 2. Inference and Serving Optimization

Moving beyond Ollama into high-throughput, cost-efficient serving.

- [ ] **vLLM** — paged attention and high-throughput serving
- [ ] **SGLang** — structured generation and fast serving
- [ ] Quantization — **GGUF**, **AWQ**, **GPTQ**, and when to use each
- [ ] Serverless GPU — **Modal**, **Baseten**, or **RunPod**

### 3. Post-Training and Fine-Tuning

Adapting models, not just consuming them, is a major differentiator.

- [ ] **LoRA** and **QLoRA** via **Unsloth** (fastest on-ramp) or **Axolotl**
- [ ] **TRL** for **DPO** and preference tuning
- [ ] Judgment: when fine-tuning beats RAG or prompting, and when it does not

### 4. Advanced Retrieval

Sharpening existing RAG skills beyond a basic vector store.

- [ ] **Rerankers** — bge-reranker, Cohere Rerank, plus hybrid (BM25 and dense)
- [ ] **pgvector** and **Qdrant** — pgvector pairs naturally with existing Postgres
- [ ] **Contextual Retrieval** and **GraphRAG** for harder corpora

### 5. Prompt and Pipeline Optimization

- [ ] **DSPy** — programmatic prompt and pipeline optimization instead of hand-tuning strings

### 6. Guardrails and LLM Security

- [ ] [Prompt-injection defense fundamentals](./prompt-injection-defense-fundamentals.md)
- [ ] **NeMo Guardrails** or **Guardrails AI**
- [ ] **Instructor** and structured outputs for reliable typed responses

### 7. Agent Standards and SDKs

Extending existing MCP knowledge and comparing orchestration approaches.

- [ ] Build a custom **MCP server**
- [ ] **A2A** (agent-to-agent) protocol
- [ ] **PydanticAI**, **OpenAI Agents SDK**, **Claude Agent SDK** — compare against LangGraph

## 3-Month Plan

| Month | Focus | Ship Something |
| :--- | :--- | :--- |
| 1 | Evaluation and observability (Langfuse, Ragas, DeepEval) plus advanced retrieval (rerankers, pgvector) | Retrofit `financial-research-analyst-agent` or `legal-document-assistant-agent` with full tracing and a RAG evaluation suite |
| 2 | Serving (vLLM, quantization, Modal) plus DSPy | Self-host a quantized model behind vLLM; convert one agent's prompts to a DSPy pipeline |
| 3 | Fine-tuning (Unsloth, QLoRA, DPO) plus guardrails | Fine-tune a small model on a domain task; add injection guardrails and structured outputs |

## The One Move That Matters Most

Take a single existing flagship repo and add **evaluation, observability, and
guardrails** to it end-to-end. That one upgrade demonstrates production
maturity more convincingly than five new demo agents.

## Reference Links

- [Langfuse](https://langfuse.com/)
- [LangSmith](https://docs.smith.langchain.com/)
- [Ragas](https://docs.ragas.io/)
- [DeepEval](https://github.com/confident-ai/deepeval)
- [Promptfoo](https://www.promptfoo.dev/)
- [vLLM](https://docs.vllm.ai/)
- [SGLang](https://github.com/sgl-project/sglang)
- [Unsloth](https://github.com/unslothai/unsloth)
- [Axolotl](https://github.com/axolotl-ai-cloud/axolotl)
- [TRL](https://huggingface.co/docs/trl)
- [Qdrant](https://qdrant.tech/)
- [pgvector](https://github.com/pgvector/pgvector)
- [DSPy](https://dspy.ai/)
- [NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails)
- [Guardrails AI](https://www.guardrailsai.com/)
- [Instructor](https://python.useinstructor.com/)
- [Model Context Protocol](https://modelcontextprotocol.io/)
