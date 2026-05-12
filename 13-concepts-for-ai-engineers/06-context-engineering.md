# 6. What is Context Engineering?

**Context engineering** is the discipline of curating *everything* the model sees in its context window — system prompts, instructions, retrieved docs, tool definitions, memory, examples, and output schemas. It is the natural successor to "prompt engineering," which focused only on the user-facing prompt.

> Prompt engineering = "what do I write?"
> Context engineering = "what does the model *see*, in what order, in what shape, and at what cost?"

---

## Why It Matters

LLM behavior is shaped almost entirely by what's in its context. Two prompts that look identical can produce wildly different results based on:

- The system prompt's framing.
- Which examples are included (and in what order).
- Whether retrieved chunks are relevant.
- Whether the output schema is enforced.
- How memory is summarized vs. truncated.

Tuning these is now most of the engineering work for production LLM apps.

---

## What Goes Into a Context Window

```
┌──────────────────────────────────────────────────────┐
│ System prompt (role, rules, persona, format)         │
├──────────────────────────────────────────────────────┤
│ Tool definitions (JSON schemas)                      │
├──────────────────────────────────────────────────────┤
│ Few-shot examples (input/output pairs)               │
├──────────────────────────────────────────────────────┤
│ Retrieved knowledge (RAG chunks, docs, schemas)      │
├──────────────────────────────────────────────────────┤
│ Long-term memory (user prefs, prior decisions)       │
├──────────────────────────────────────────────────────┤
│ Conversation history (summarized + recent turns)     │
├──────────────────────────────────────────────────────┤
│ Current user message                                 │
└──────────────────────────────────────────────────────┘
```

Each layer is a knob.

---

## Core Techniques

### 1. **Compression**

- Summarize old turns once they fall out of relevance.
- Replace verbose tool outputs with concise representations.
- Strip whitespace, comments, repeated boilerplate.

### 2. **Prioritization & Ordering**

- Put critical instructions at the **top** (system prompt) or **bottom** (just before the answer) — models pay more attention there.
- Beware *"lost in the middle"* — content in the middle of a long context gets ignored.

### 3. **Retrieval shaping**

- Choose chunk size and k carefully.
- Rerank to put the best chunks closest to the question.
- Deduplicate near-identical chunks.

### 4. **Few-shot selection**

- Pick examples *similar to the current input* (dynamic few-shot).
- Cover edge cases the model usually fails on.
- Keep examples short — they consume tokens linearly.

### 5. **Output schema constraints**

- JSON schema, regex, or grammar-constrained decoding.
- Tells the model *exactly* what shape you expect.

### 6. **Prompt caching**

- Cache the static prefix (system prompt, tools, docs).
- Massive cost and latency savings (Anthropic, OpenAI, Google all support).

### 7. **Memory architecture**

- **Short-term:** scratchpad in current context.
- **Long-term:** vector store + selective recall.
- **Procedural:** reusable skills/tools.

---

## The Token Budget

Every context-window slot is a tradeoff:

| Item | Typical share | Notes |
|------|---------------|-------|
| System prompt | 1–5% | Stable, cache it |
| Tool schemas | 5–20% | Trim unused tools |
| Few-shot examples | 5–15% | Only when needed |
| RAG context | 30–60% | Often the biggest spender |
| Conversation history | 10–30% | Summarize aggressively |
| User input + scratch | remainder | |

You're a budget allocator. Spend tokens where they buy the most quality.

---

## The "Context Rot" Problem

As context grows:

- Latency goes up (linearly to quadratically).
- Cost goes up.
- Quality often goes *down* past a threshold (~50–80k tokens for many models).

**Long-context ≠ free context.** Just because the model accepts 1M tokens doesn't mean stuffing it is the right move.

---

## Practical Patterns

- **Retrieval > stuffing** for large corpora.
- **Summarize-and-forget** for long agent traces.
- **Per-task tool subsetting** — don't expose all 50 tools at once.
- **Structured > prose** — JSON, tables, and bullet points are higher signal-per-token.
- **Cache aggressively** — same prefix across many calls is free after the first.

---

## How It Differs From Prompt Engineering

| Prompt engineering | Context engineering |
|---------------------|---------------------|
| One string | Many sources composed dynamically |
| Static | Built per-request |
| Tweaking wording | Architecting the information flow |
| "What should I say?" | "What should the model see?" |

---

## Further Reading

- Anthropic — *Prompt caching*, *Contextual Retrieval*
- "Lost in the Middle" (Liu et al., 2023)
- LangChain & LlamaIndex docs on context construction
