# 9. LLM Concepts — A Deep Dive

A foundational tour of how Large Language Models actually work — the mental model every AI engineer should carry around. Skipping this makes everything else (RAG, agents, evals) shaky.

---

## What Is an LLM?

An LLM is a neural network — almost always a **decoder-only Transformer** — trained to predict the **next token** given previous tokens. From this single objective, surprising capabilities emerge: translation, code, reasoning, instruction following.

```
Input tokens ─► Transformer layers ─► Probability distribution over next token
```

Sample, append, repeat → text appears.

---

## Tokens & Tokenization

Models don't see characters or words — they see **tokens**, sub-word units produced by **Byte-Pair Encoding (BPE)** or similar.

- "tokenization" → `["token", "ization"]`
- "🍕" → may be 1–3 tokens depending on encoding
- Typical: ~4 chars or ~0.75 words per token in English

Why this matters:
- **Context limits** are measured in tokens.
- **Cost** is per-token.
- Different languages tokenize at different rates (English most efficient).

---

## The Transformer in 30 Seconds

For each layer:

1. **Attention** — every token looks at every previous token, weighted by relevance.
2. **Feed-forward** — a 2-layer MLP applied to each position.
3. **Residual + LayerNorm** — stabilize training.

Stack 30–100+ such layers. Add positional encoding so order matters. That's the architecture.

Key building blocks:
- **Embeddings** — token IDs → high-dim vectors.
- **Multi-head attention** — N parallel attention "heads."
- **Causal mask** — token *t* can only see tokens 1..t-1 (autoregressive).
- **Positional encoding** — RoPE in modern models.

---

## Training Stages

```
1. Pretraining (weeks–months on trillions of tokens)
        ▼
2. SFT (Supervised Fine-Tuning on instruction data)
        ▼
3. Preference tuning (RLHF / DPO / RLAIF)
        ▼
4. (Optional) Tool use / agent training
        ▼
5. Safety / red-teaming / final polish
```

- **Pretraining** — predict next token on the open web, books, code.
- **SFT** — show the model good Q&A pairs to teach format and helpfulness.
- **RLHF** — train a reward model from human preferences; tune the LLM with PPO.
- **DPO** — newer, simpler alternative to RLHF.
- **Constitutional AI / RLAIF** — use AI feedback against a written constitution.

---

## Inference Knobs

| Knob | What it does | Typical value |
|------|--------------|---------------|
| **temperature** | Higher = more random | 0.0 deterministic, 0.7 creative |
| **top-p (nucleus)** | Sample from smallest set whose probs sum to p | 0.9 |
| **top-k** | Restrict to k highest-prob tokens | 40 |
| **max_tokens** | Hard cap on output length | task-specific |
| **stop sequences** | End generation when seen | `"</answer>"`, `\n\n` |
| **frequency / presence penalties** | Discourage repetition | 0–1 |

---

## Emergent Capabilities

Behaviors not explicitly trained, that appear past a scale threshold:

- **In-context learning** — learn a task from examples in the prompt.
- **Chain-of-thought** — produce intermediate reasoning steps.
- **Tool use / function calling** — output structured calls.
- **Instruction following** — execute novel directives.
- **Multilingual transfer** — apply skills across languages.

---

## Reasoning Models

A 2024–25 development: models trained with RL on **verifiable rewards** (math, code) to think *before* answering — producing long internal chains of thought.

- OpenAI o1 / o3, Claude reasoning, DeepSeek-R1, Gemini Thinking.
- Trade more inference compute for better reasoning.
- Stronger on math, coding, planning; weaker cost/latency.

---

## Limitations

- **Hallucination** — confident plausible falsehoods.
- **Cutoff date** — frozen knowledge of the world.
- **No persistent memory** between sessions (without infra).
- **Token-level reasoning** — can be tripped by counting, exact arithmetic, character manipulation.
- **Sensitivity to phrasing** — small prompt changes shift outputs.
- **Bias** — reflects training data biases.

---

## Capabilities by Model Class (rough 2025–26)

| Class | Examples | Strengths |
|-------|----------|-----------|
| Frontier reasoning | Claude Opus 4.7, o3, Gemini 2.5 Ultra | Hardest tasks |
| Frontier general | Claude Sonnet 4.6, GPT-4.x | Daily driver |
| Small/fast | Haiku 4.5, GPT mini, Flash | High volume |
| Open weights | Llama 4, Qwen 3, Mistral | Self-host, fine-tune |
| Multimodal | Most frontier models | Vision, audio, video |

---

## Cost & Latency Intuition

- Input tokens are **cheaper** than output tokens (often 3–5×).
- Prompt caching can cut input cost 90% on repeated prefixes.
- Streaming helps perceived latency, not total latency.
- Reasoning models can be 10–100× slower per task — worth it on hard problems only.

---

## Mental Models That Help

- **"Compressed Internet"** — the model is a lossy compression of its training data.
- **"Stochastic parrot... that does math now"** — pattern-matching with surprising depth.
- **"Reasoning is just longer outputs"** — chain-of-thought is the model thinking out loud.
- **"Context is RAM"** — short, fast, expensive; manage carefully.

---

## Further Reading

- *The Illustrated Transformer* — Jay Alammar
- Andrej Karpathy's "Let's build GPT" video
- Anthropic's interpretability research blog
- *Attention Is All You Need* (Vaswani et al., 2017)
