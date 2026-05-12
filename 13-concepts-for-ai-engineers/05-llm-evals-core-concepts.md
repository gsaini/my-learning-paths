# 5. LLM Evals — Core Concepts

**LLM evals** are how you measure whether your model, prompt, or agent actually works — and whether a change makes it better or worse. Without evals, prompt engineering is vibes; with evals, it's engineering.

> Rule of thumb: **if you can't measure it, you can't ship it.** A prompt without an eval set is a prayer.

---

## Why Evals Are Hard

- Outputs are **free-form text**, not numbers.
- Many valid answers exist — there's no single "correct" one.
- Quality is **multidimensional**: accurate AND helpful AND safe AND fast AND cheap.
- Models drift; behavior shifts on every prompt or version change.

---

## The Eval Hierarchy

```
        Production traces (real users)         ← Highest signal, slow
                  ▲
        A/B tests / shadow traffic
                  ▲
        Human eval on golden set
                  ▲
        LLM-as-judge on golden set
                  ▲
        Automated metrics (regex, schema, BLEU)  ← Cheapest, fastest
```

Use the cheap layers continuously; reserve human eval for high-stakes changes.

---

## Categories of Evals

### 1. Reference-based (you have a ground truth)

- **Exact match / regex** — for structured outputs, IDs, codes.
- **BLEU / ROUGE / METEOR** — n-gram overlap (translation, summarization).
- **F1, accuracy, recall** — for classification.
- **HumanEval / MBPP** — code passes unit tests.

### 2. Reference-free (no single right answer)

- **LLM-as-judge** — a strong model rates the output on rubrics.
- **Pairwise comparison** — "Is A better than B?" — more reliable than absolute scores.
- **Embedding similarity** — semantic closeness to a reference.

### 3. Human eval

- Side-by-side comparisons.
- Likert scales on rubrics (helpfulness, faithfulness, tone).
- Expensive but the gold standard for nuanced quality.

### 4. Task-specific

- **HumanEval / SWE-Bench** for code.
- **MMLU / GPQA** for knowledge.
- **RAGAS** for RAG faithfulness.
- **Tool-call accuracy** for agents.

---

## What to Measure

| Dimension | What it asks | Example metric |
|-----------|--------------|----------------|
| **Correctness** | Is the answer factually right? | Exact match, judge score |
| **Faithfulness** | Does it stick to the provided context? | RAGAS faithfulness |
| **Helpfulness** | Does it solve the user's problem? | Pairwise win-rate |
| **Safety** | Does it refuse harmful requests / avoid PII leaks? | Red-team pass rate |
| **Format** | Does it match the schema? | JSON-schema validation |
| **Latency** | p50 / p95 response time | ms |
| **Cost** | $ per 1k requests | $ |

---

## Building an Eval Set

1. **Start with 20–50 cases** drawn from real user traffic or expected use cases.
2. Cover **golden paths and edge cases** — adversarial inputs, ambiguity, long inputs.
3. Tag each case with metadata (intent, difficulty, language).
4. Define **pass criteria** per case (exact answer, rubric, schema).
5. Grow it weekly from production failures.

A 200-case eval set you trust is worth more than a 10,000-case one you don't.

---

## LLM-as-Judge: Done Right

- Use a **stronger** model than the one being evaluated.
- Give the judge a **rubric**, not a vibe ("Score 1–5 on faithfulness, where 5 = every claim is supported by the context").
- Prefer **pairwise** comparisons (A vs B) — humans and LLMs both rank better than they score.
- **Calibrate** the judge against human ratings on a sample.
- Watch for **position bias, length bias, and verbosity bias** — randomize order, control length.

---

## CI / Regression Testing

- Treat the eval suite like unit tests.
- Run on every prompt change, every model version bump.
- Block deploys on regressions in critical metrics.
- Track scores over time on a dashboard.

---

## Common Pitfalls

- **Overfitting to the eval set** — model improves on the test, regresses in prod.
- **Static evals on a moving world** — refresh as use cases evolve.
- **Single-number obsession** — you usually need a Pareto view across cost, quality, latency.
- **Ignoring variance** — run each case 3–5× when stochastic; report mean ± stdev.

---

## Tooling

- **Open source:** Promptfoo, DeepEval, RAGAS, Inspect, OpenAI Evals.
- **Hosted:** Braintrust, Langsmith, Arize Phoenix, Humanloop, Weights & Biases.
- **Roll-your-own:** A pytest suite + a judge prompt + a CSV dataset gets you 80% of the value.

---

## Further Reading

- Anthropic — *Building evals* posts
- "Your AI product needs evals" — Hamel Husain
- OpenAI Evals repo
