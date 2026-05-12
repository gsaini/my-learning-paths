# 12. How AI Chat Assistants Work

A walk through the **anatomy of a production chat assistant** — ChatGPT, Claude, Gemini, or your own custom assistant. Beneath the simple chat box lies a distributed system juggling context, tools, streaming, memory, safety, and cost.

---

## High-Level Architecture

```
┌──────────────┐    HTTPS/WS   ┌─────────────────┐
│  Web / App   │ ◄────────────►│   API Gateway   │
│   Frontend   │   SSE stream  │  (auth, rate)   │
└──────────────┘               └────────┬────────┘
                                        │
                              ┌─────────▼──────────┐
                              │   Orchestrator     │
                              │ (prompt assembly,  │
                              │  tools, RAG, mem)  │
                              └────┬───────┬───────┘
                                   │       │
                         ┌─────────▼─┐  ┌──▼─────────┐
                         │   LLM     │  │  Tools /   │
                         │  Provider │  │  Plugins   │
                         └───────────┘  └────────────┘
                                   │
                         ┌─────────▼──────┐
                         │  Vector DB +   │
                         │  RDS / Cache   │
                         └────────────────┘
```

---

## Request Lifecycle

1. **User types** a message; client sends it via HTTPS or WebSocket.
2. **Gateway** authenticates, rate-limits, attaches user identity.
3. **Orchestrator** assembles the full context:
   - System prompt
   - User profile / long-term memory
   - Conversation history (summarized + recent)
   - Retrieved documents (RAG)
   - Tool schemas
4. Sends to **LLM provider** (Anthropic, OpenAI, in-house, etc.).
5. LLM **streams** tokens back via Server-Sent Events (SSE) or WebSocket.
6. If the LLM emits a **tool call**, the orchestrator executes it and feeds the result back into the loop.
7. Final answer is rendered token-by-token in the UI.
8. Conversation is **persisted** to the database.

---

## Streaming

Streaming is what makes chat assistants feel fast.

- **SSE (Server-Sent Events)** — one-way push from server to client; HTTP-friendly.
- **WebSocket** — bidirectional; nice when the user can interrupt or send signals mid-stream.
- Tokens are rendered as they arrive — perceived latency drops 5–10×.
- Backpressure, reconnection, and partial-token rendering are non-trivial details.

---

## Stateful Conversation

LLMs have **no built-in memory** between calls. State is engineered:

- **Conversation history** — past messages re-sent each call.
- **Truncation strategy** — drop oldest, summarize middle, keep recent + system.
- **Compression** — replace long history with a summary when token budget hits a threshold.
- **Persistent memory** — facts about the user pulled from a separate store.

Naive chat apps blow up at 30+ messages. Production ones manage history actively.

---

## Tool / Function Calling

Modern assistants don't just chat — they act.

- The LLM sees tool **JSON schemas** in its context.
- It can output a structured tool call instead of plain text.
- The orchestrator executes the tool and returns the result as a new message.
- Loop until the model produces a normal text reply.

Tools commonly include: web search, code execution, image generation, database queries, calendar, email, internal company APIs.

---

## RAG Inside a Chat Assistant

Whenever the assistant needs knowledge it wasn't trained on:

1. Embed the user message.
2. Retrieve top-k chunks from a vector DB.
3. Insert them into the prompt with a citation directive.
4. The model answers grounded in those chunks.

Used for: company knowledge bases, product docs, "Chat with PDF," up-to-date info.

---

## Memory

A spectrum:

| Type | Lives in | Lifetime | Example |
|------|----------|----------|---------|
| **Working** | Current context window | One turn | Recent messages |
| **Session** | Server cache | Minutes–hours | Today's conversation |
| **Long-term** | DB + vector store | Forever | "User prefers metric units" |
| **Procedural** | Codebase | Indefinite | Hard-coded skills/tools |

Letting the model write to memory is powerful but needs **boundaries**: what to save, what to forget, what to surface.

---

## Multimodal Add-ons

- **Image input** — vision-capable models accept image URLs / base64.
- **File upload** — PDFs, spreadsheets, code; usually parsed and chunked.
- **Audio in/out** — speech-to-text in, TTS out.
- **Video** — frames sampled and treated as images.

---

## Safety & Moderation

- **Input moderation** — block prompt injection, abusive content.
- **Output moderation** — filter harmful, copyrighted, or PII content.
- **Refusal policies** — when to decline.
- **Red-teaming evals** — adversarial test suites.
- **Logging & audit** — for compliance and abuse review.

---

## Cost & Latency Engineering

- **Prompt caching** — reuse the system prompt prefix.
- **Tiered routing** — cheap model for trivial requests; flagship for hard ones.
- **Speculative decoding** — small model drafts, big model verifies.
- **Batching** — coalesce concurrent requests on the LLM side.
- **Context trimming** — every token costs.
- **Pre-warming** — keep a warm pool of LLM connections.

---

## Reliability

- **Retries** with exponential backoff.
- **Circuit breakers** on LLM provider outages.
- **Fallback models** — when the primary fails.
- **Idempotency keys** for tool calls.
- **Graceful degradation** — partial answers > no answer.

---

## Anatomy of a "Simple" Question

User: *"What's the status of order #4429?"*

Behind the scenes:

1. Auth → identify user.
2. Load user profile + last 5 messages.
3. Recognize order intent → call `lookup_order(id="4429")`.
4. Tool returns: `{status: "shipped", eta: "2026-05-08"}`.
5. LLM composes a natural reply with citation.
6. Stream reply to client.
7. Persist conversation; update analytics.

All in ~1.5 seconds.

---

## Further Reading

- OpenAI / Anthropic API docs (chat, streaming, tool use, prompt caching)
- "Building LLM-Powered Apps" — various practitioner write-ups
- LangChain / LlamaIndex chat tutorials
