# 8. How to Design an AI Agent

A practical, step-by-step playbook for designing a production-quality AI agent. Treat this like a checklist — skipping steps is the #1 cause of agents that look great in demos and fall over in prod.

> Two sentences to remember: **An agent is only as good as its tools and its evals. Everything else is decoration.**

---

## Step 1 — Define the Job

Write a one-paragraph spec answering:

- What outcome does this agent produce?
- Who is the user?
- What's the success metric? (Concrete and measurable.)
- What's explicitly *out of scope*?

Bad: *"Help users with their finances."*
Good: *"Given a user's transactions for last month, produce a 5-bullet summary categorized by spending area, with month-over-month deltas."*

---

## Step 2 — Map the Task Surface

- List the **inputs** (text, images, files, structured data).
- List the **outputs** (markdown, JSON, side effects).
- List the **subtasks** the agent might need to perform.
- For each subtask, decide: built-in LLM capability, or a tool?

---

## Step 3 — Pick the Model

Trade off:

| Need | Lean toward |
|------|-------------|
| Deep reasoning, long horizon | Largest reasoning model (Opus, o3) |
| Fast, cheap, simple tasks | Haiku, Mini, Flash |
| Tool use, function calling | Any frontier — verify on your tasks |
| Multimodal | Models with strong vision (Claude, Gemini, GPT-4o) |
| On-device / private | Open weights (Llama, Mistral, Qwen) |

Always **eval at least 2 models** on your task before committing.

---

## Step 4 — Design the Tools

Tools are the most important design surface. For each tool:

- **One clear job.** Don't make `do_thing` that takes a 50-field config.
- **Idempotent when possible.** Calling twice should be safe.
- **Clear schemas** with JSON Schema, with descriptions on every field.
- **Concise outputs** — token-efficient. Truncate huge responses.
- **Helpful errors** — the agent uses error messages to recover.
- **Safety:** destructive tools require confirmation or stay out of agent reach.

A small, sharp toolkit beats a kitchen sink.

---

## Step 5 — Design the Memory

- **Working memory** — current conversation / scratchpad. Free.
- **Short-term** — last N turns or rolling summary.
- **Long-term** — vector store for episodic recall, structured DB for facts.
- **User memory** — preferences, past tasks. Make it inspectable and editable.

Decide: when does the agent **read** memory? When does it **write**? Who **clears** stale entries?

---

## Step 6 — Pick the Control Loop

| Loop | Use it when |
|------|-------------|
| **ReAct** | Default for most agents |
| **Plan-and-execute** | Long horizon, dependent steps |
| **Reflection** | Output quality matters more than speed |
| **Graph / state machine** | Predictable flow with branching (LangGraph) |

Add **stop conditions** explicitly:

- Max steps (e.g., 25)
- Token / cost budget
- Loop detection (same call repeated)
- Explicit `final_answer` signal
- Human cancel

---

## Step 7 — Add Guardrails

- **Input validation** — schemas, length limits, content checks.
- **Output validation** — JSON schema, allowed values, regex.
- **Permission gates** — destructive actions require human approval.
- **Rate limits** — per-user, per-tool, per-session.
- **PII / secrets handling** — redact in logs and traces.
- **Refusal policy** — when to bail out vs proceed.

---

## Step 8 — Build the Eval Harness

Before you ship:

1. Build a **golden set** of 30–100 tasks with expected outcomes.
2. Define **per-task pass criteria** (exact match, schema, judge rubric).
3. Add **regression tests** for past failures.
4. Run on every prompt/model/tool change.
5. Track scores over time.

If it isn't in the eval, it isn't real.

---

## Step 9 — Add Observability

- **Per-step logs** — input, output, tool calls, latency, cost.
- **Trace IDs** — link every step of a session.
- **Replay capability** — re-run a session deterministically.
- **Dashboards** — error rate, p95 latency, cost per task, success rate.

Tools: Langsmith, Braintrust, Arize Phoenix, Weights & Biases.

---

## Step 10 — Plan for Failure

- What happens when a tool times out?
- What if the LLM returns malformed JSON?
- What if the user cancels mid-task?
- What if the model loops?
- Graceful degradation > silent failure.

---

## A Sketch in Code (pseudocode)

```python
agent = Agent(
    model="claude-opus-4-7",
    system_prompt=SYSTEM,
    tools=[search, read_file, write_file, run_tests],
    memory=VectorMemory(scope="user_123"),
    max_steps=25,
    cost_cap_usd=2.0,
    on_destructive=require_human_approval,
    output_schema=TaskResult,
)
result = agent.run(user_task)
```

---

## Common Mistakes

- Building tools after the prompt instead of before.
- No evals → invisible regressions.
- Letting the agent loop forever to "be helpful."
- Coupling the agent to a specific model with no fallback.
- Pasting whole files into context instead of designing retrieval.

---

## Further Reading

- Anthropic — *Building effective agents*
- LangGraph design guide
- *Designing Data-Intensive Applications* (the principles transfer surprisingly well)
