# 7. Multi-Agent Architectures

A **multi-agent system** is one where multiple specialized LLM-powered agents collaborate to accomplish a task that would be too complex, too long, or too unreliable for a single agent.

> Heuristic: don't reach for multi-agent until a single agent demonstrably can't do the job. Coordination overhead is real.

---

## Why Multi-Agent?

- **Specialization** — a "researcher" agent and a "writer" agent each have tighter, simpler prompts.
- **Parallelism** — independent subtasks run concurrently, reducing wall-clock time.
- **Context isolation** — each agent works in its own context window, avoiding bloat.
- **Modularity** — swap one agent without rewriting the others.

The cost: more tokens, more latency-per-handoff, more failure modes (handoff drift, context loss).

---

## The Five Common Patterns

### 1. Orchestrator–Worker
A lead agent decomposes the task, dispatches subtasks to workers, and synthesizes results.

```
                ┌──────────────┐
                │ Orchestrator │
                └──────┬───────┘
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
   ┌─────────┐   ┌─────────┐   ┌─────────┐
   │Worker A │   │Worker B │   │Worker C │
   └─────────┘   └─────────┘   └─────────┘
```

Used by Anthropic's research agent. **Best for:** parallel research, multi-source data gathering.

---

### 2. Hierarchical / Supervisor
Tree of agents — managers above, specialists below. Useful when subtasks themselves need decomposition.

```
       Supervisor
       /        \
   Manager A   Manager B
    /  \         |
   W1  W2        W3
```

**Best for:** large, multi-domain problems.

---

### 3. Network / Swarm
Peer agents that hand off to each other based on task type. No fixed hierarchy.

```
   [Agent A] ◄──► [Agent B] ◄──► [Agent C]
       ▲              ▲              ▲
       └──────────────┴──────────────┘
```

Used by **OpenAI Swarm**, **CrewAI**. **Best for:** customer-support style routing.

---

### 4. Sequential Pipeline
Agents in a fixed chain — each transforms the output of the previous one.

```
[Researcher] → [Outliner] → [Writer] → [Editor]
```

**Best for:** content generation, ETL-style flows.

---

### 5. Debate / Consensus
Multiple agents argue or vote; an aggregator picks the best answer.

**Best for:** high-stakes reasoning, reducing hallucination.

---

## Communication Models

- **Shared memory / blackboard** — all agents read/write a common scratchpad.
- **Message passing** — explicit messages between agents (like microservices).
- **Tool-call as handoff** — the orchestrator "calls" a worker as if it were a tool.

---

## Coordination Patterns

- **Map-reduce** — split work, run in parallel, aggregate.
- **Routing** — classifier picks the right specialist agent.
- **Reflection chains** — an "actor" agent acts; a "critic" agent reviews; loop.
- **Voting / ensembling** — N agents answer; majority or judge wins.

---

## When Multi-Agent Helps

- The task has **independent parallelizable subtasks** (e.g., research 5 companies).
- Subtasks need **different tools or expertise**.
- A single agent's context is **bursting at the seams**.
- You need **role separation for safety** (e.g., a "verifier" that can't take actions).

## When Multi-Agent Hurts

- Task is **mostly sequential** — overhead with no benefit.
- Latency matters and tasks could be done in one agent.
- You don't have **strong evals** — debugging multi-agent failure is brutal.
- Agents share most of the context — you're paying tokens twice.

---

## Frameworks

| Framework | Style |
|-----------|-------|
| **LangGraph** | Explicit graph/state machine |
| **CrewAI** | Role-based, scripted crews |
| **AutoGen** | Conversational agents, group chat |
| **OpenAI Swarm** | Lightweight handoffs (now `agents` SDK) |
| **MetaGPT** | Software-team metaphor (PM, dev, QA) |
| **Anthropic Agent SDK** | Claude-native subagents |

---

## Anti-Patterns

- "Multi-agent because it's cool" — start with one agent.
- **Token amplification** — same context shipped to every agent.
- **Telephone game** — quality degrades as info passes through agents.
- **No supervisor** — agents bicker forever.
- **Unbounded delegation** — workers spawn workers spawn workers.

---

## Designing a Multi-Agent System

1. List all subtasks. Are they truly independent?
2. Define each agent's **single responsibility**.
3. Define handoff contracts (input/output schemas).
4. Add a stop condition and budget per agent.
5. Add a top-level synthesizer.
6. Build evals for the whole system AND each agent.

---

## Further Reading

- Anthropic — *How we built our multi-agent research system*
- *AutoGen* paper (Wu et al., 2023)
- LangGraph multi-agent tutorials
