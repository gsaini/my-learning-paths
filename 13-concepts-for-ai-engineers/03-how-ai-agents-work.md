# 3. How AI Agents Work

An **AI agent** is an LLM placed inside a loop, given **tools** and **memory**, and told to pursue a goal. Unlike a chatbot — which responds turn-by-turn — an agent *acts on the world*: it calls APIs, writes files, browses the web, and decides for itself when it is done.

> Agent = LLM + Loop + Tools + Memory + Goal.

---

## The Core Loop

```
        ┌──────────────────────────────────────┐
        │                                      │
        ▼                                      │
   ┌──────────┐    ┌──────────┐    ┌────────────┐
   │ Perceive │ ─► │  Reason  │ ─► │  Act (tool) │
   └──────────┘    └──────────┘    └────────────┘
        ▲                                │
        │                                ▼
        └──── Observe (tool result) ◄────┘
```

1. **Perceive** — read user input, environment state, prior steps.
2. **Reason** — decide the next action (often using chain-of-thought).
3. **Act** — invoke a tool: API call, code execution, file write.
4. **Observe** — capture the result; update working memory.
5. **Repeat** until the goal is met or a stop condition triggers.

---

## The Five Building Blocks

| Block | What it is | Example |
|-------|------------|---------|
| **Brain (LLM)** | The reasoning engine | Claude, GPT, Gemini |
| **Tools** | Functions the agent can call | `web_search`, `run_python`, `send_email` |
| **Memory** | Short- and long-term state | Scratchpad, vector DB, user profile |
| **Planner** | Optional explicit plan step | "First do A, then B, then C" |
| **Executor** | Loop that calls tools and feeds results back | Agent runtime / orchestrator |

---

## Loop Styles

- **ReAct (Reason + Act)** — interleave a "thought" with each tool call. Simple, robust default.
- **Plan-and-Execute** — generate a full plan first, then execute steps. Good for long tasks.
- **Reflexion / Self-Critique** — after a step, the agent critiques its work and retries.
- **Tree-of-Thought** — explore multiple branches; pick the best.
- **Graph-based (LangGraph)** — explicit state machine with nodes and edges.

---

## Memory Types

- **Working memory** — the conversation history / scratchpad in the context window.
- **Episodic memory** — past conversations and tasks stored in a vector DB.
- **Semantic memory** — facts about the user, the world, the codebase.
- **Procedural memory** — learned skills or recipes (often as prompts/tools).

---

## Stop Conditions

A real agent must know when to *stop*:

- Goal achieved (model emits a `final_answer` / `done` signal).
- Max iterations reached (e.g., 25 steps).
- Token / cost budget exceeded.
- Repeated identical tool calls (loop detection).
- Human cancels.

Without these, agents burn money and spiral.

---

## Agent vs Chatbot vs Workflow

| | Chatbot | Workflow | Agent |
|---|---------|----------|-------|
| Decides next step? | No | No (hardcoded) | **Yes** |
| Calls tools? | Sometimes | Yes (fixed) | Yes (dynamically) |
| Variable steps? | No | No | **Yes** |
| Risk of looping? | Low | Low | **Higher** |
| Best when... | Q&A | Predictable pipelines | Open-ended tasks |

Anthropic's guidance: **start with workflows; only escalate to agents when the task truly needs autonomy** — agents trade reliability for flexibility.

---

## Real-World Examples

- **Coding agents** — Claude Code, Cursor agent, Devin, Aider.
- **Research agents** — Anthropic's research feature, Perplexity Pro Search.
- **Customer support agents** — Sierra, Decagon.
- **Browser agents** — Operator, Browser Use, Playwright agents.
- **Computer-use agents** — Claude's computer use, OpenAI Operator.

---

## What Makes a Good Agent

1. **Narrow, well-defined job** — broad goals fail.
2. **Strong tools** with clear, idempotent contracts.
3. **Tight feedback loops** — let the agent see errors and retry.
4. **Guardrails** — confirmation for destructive actions; output schemas; cost caps.
5. **Observability** — every step logged for replay and debugging.
6. **Evals** — golden tasks measured on every prompt/model change.

---

## Failure Modes to Watch For

- **Tool drift** — the agent forgets it has a tool or uses it incorrectly.
- **Context bloat** — long traces dilute attention; the agent loses the plot.
- **Reward hacking** — agent satisfies the literal goal but not the intent.
- **Premature completion** — declares success without verifying.
- **Doom loops** — calls the same failing tool repeatedly.

---

## Further Reading

- Anthropic — "Building effective agents" (2024)
- LangChain / LangGraph docs
- ReAct paper (Yao et al., 2022)
