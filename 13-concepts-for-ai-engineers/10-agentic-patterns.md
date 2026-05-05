# 10. Agentic Patterns, Simply Explained

A handful of reusable design patterns underlie almost every successful agent. Knowing them lets you compose agents the way backend engineers compose microservice patterns.

---

## 1. ReAct — Reason + Act

The default agent pattern. The model alternates between **thought** and **action**, observing results between each.

```
Thought: I need yesterday's revenue. I'll call the analytics tool.
Action: query_revenue(date="2026-05-03")
Observation: $128,402
Thought: Got it. I'll respond to the user.
Final answer: Yesterday's revenue was $128,402.
```

**Strengths:** Simple, transparent, easy to debug.
**Watch out for:** Verbose traces; the model may loop on bad observations.

---

## 2. Reflection — Self-Critique

After producing an output, the agent (or a "critic" agent) reviews its own work and revises it.

```
Draft → Critique → Revise → (loop until good or N iterations)
```

**Use when:** quality matters more than latency (writing, code review, hard reasoning).
**Watch out for:** infinite polish — cap iterations.

---

## 3. Tool Use / Function Calling

The model emits a **structured call** (JSON with name + args), the host executes it, and feeds the result back.

- The atomic unit of agent capability.
- Tools should be **idempotent**, **well-typed**, and **few**.
- The agent's effectiveness is largely a function of tool design.

---

## 4. Planning

The agent first generates a multi-step **plan**, then executes each step.

```
Plan:
1. Search the web for X
2. Read the top-3 sources
3. Summarize in 5 bullets

Executing step 1...
Executing step 2...
```

**Use when:** the task has clear sub-steps, or you want the user to approve the plan before execution.
**Variant:** plan-and-execute (full plan up front) vs incremental planning (re-plan after each step).

---

## 5. Multi-Agent Collaboration

Specialized roles cooperate:

- **Researcher** gathers facts.
- **Analyst** synthesizes.
- **Writer** drafts.
- **Editor** refines.
- **Critic** reviews.

Use only when subtasks are genuinely heterogeneous; otherwise overhead dominates.

---

## 6. Routing

A classifier (often an LLM call) inspects the input and dispatches to the right specialist agent or prompt.

```
                ┌─► Billing agent
User → Router ──┼─► Tech-support agent
                └─► Sales agent
```

**Use when:** broad domain with distinct sub-domains. Common in customer support.

---

## 7. Map-Reduce

Split work across N parallel agent calls, then aggregate.

```
[Doc1] [Doc2] [Doc3] [Doc4]
   │      │      │      │
   ▼      ▼      ▼      ▼
[Sum1] [Sum2] [Sum3] [Sum4]
   └──────┴──────┴──────┘
              ▼
      [Final summary]
```

**Use when:** processing many independent items (docs, transactions, tickets).

---

## 8. Tree-of-Thought

Explore multiple reasoning branches and pick the best.

```
              Root
             /  |  \
           B1  B2  B3
           /    |    \
          ...  ...  ...
```

**Use when:** problems with multiple plausible approaches; expensive but powerful.

---

## 9. Self-Consistency

Sample N answers at higher temperature; take the majority vote (or judge-picked best).

**Use when:** the task has a verifiable or comparable answer (math, classification).

---

## 10. Rewriting / Query Decomposition

Before retrieving, the agent **rewrites** or **decomposes** the user's query.

- "What's our churn rate?" → "What is monthly churn rate? what is annual churn?"
- Improves retrieval recall on ambiguous questions.

---

## 11. Compression / Summarization Loop

For long-running agents, periodically summarize the trace and replace the full history with the summary. Keeps context lean.

---

## 12. Human-in-the-Loop

The agent **pauses for confirmation** before high-risk actions (sending email, deleting data, paying money). Non-negotiable for production.

---

## How to Combine Patterns

A real agent usually layers several patterns:

> **Routing** → **Planning** → **ReAct (with tools)** → **Reflection** → **Final answer**

Start simple (just ReAct + tools) and add patterns *only* when an eval gap shows you need them.

---

## Decision Cheat Sheet

| Symptom | Try this pattern |
|---------|-------------------|
| Agent answers but quality is poor | Reflection |
| Agent ignores parts of task | Planning |
| Long inputs / many docs | Map-Reduce |
| Several distinct user intents | Routing |
| Hard math / logic | Tree-of-Thought + Self-Consistency |
| Ambiguous queries | Query rewriting |
| Trace gets too long | Summarization loop |

---

## Further Reading

- Anthropic — *Building effective agents*
- ReAct (Yao et al.), Reflexion (Shinn et al.), Tree-of-Thought (Yao et al.)
- LangGraph cookbooks
