# 13 Concepts for AI Engineers

A learning path covering the foundational concepts every AI engineer should know — from MCP and RAG to agents, evals, and vector databases.

> Inspired by the *System Design Playbook* by Neo Kim — newsletter.systemdesign.one

---

## Index

| #  | Topic | File |
|----|------|------|
| 1  | How MCP Works | [01-how-mcp-works.md](01-how-mcp-works.md) |
| 2  | How RAG Works | [02-how-rag-works.md](02-how-rag-works.md) |
| 3  | How AI Agents Work | [03-how-ai-agents-work.md](03-how-ai-agents-work.md) |
| 4  | AI Coding Workflow 101 | [04-ai-coding-workflow-101.md](04-ai-coding-workflow-101.md) |
| 5  | LLM Evals — Core Concepts | [05-llm-evals-core-concepts.md](05-llm-evals-core-concepts.md) |
| 6  | What is Context Engineering? | [06-context-engineering.md](06-context-engineering.md) |
| 7  | Multi-Agent Architectures | [07-multi-agent-architectures.md](07-multi-agent-architectures.md) |
| 8  | How to Design an AI Agent | [08-how-to-design-an-ai-agent.md](08-how-to-design-an-ai-agent.md) |
| 9  | LLM Concepts — A Deep Dive | [09-llm-concepts-deep-dive.md](09-llm-concepts-deep-dive.md) |
| 10 | Agentic Patterns, Simply Explained | [10-agentic-patterns.md](10-agentic-patterns.md) |
| 11 | What is Reinforcement Learning? | [11-reinforcement-learning.md](11-reinforcement-learning.md) |
| 12 | How AI Chat Assistants Work | [12-how-ai-chat-assistants-work.md](12-how-ai-chat-assistants-work.md) |
| 13 | How Vector Databases Work | [13-how-vector-databases-work.md](13-how-vector-databases-work.md) |

---

## Suggested Reading Order

**Foundations first** → 9 (LLM Concepts) → 6 (Context Engineering) → 2 (RAG) → 13 (Vector DBs)

**Then build agents** → 3 (Agents) → 10 (Patterns) → 8 (Design) → 7 (Multi-Agent) → 1 (MCP)

**Then ship it** → 4 (Coding Workflow) → 5 (Evals) → 12 (Chat Assistants) → 11 (RL)

---

## Local Setup (Markdown Linting)

This repo lints staged markdown files on commit using [markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2) + [lint-staged](https://github.com/lint-staged/lint-staged), wired up as a native git hook in [`../.githooks/`](../.githooks/) (no husky). After cloning, run:

```sh
pnpm install
```

`pnpm install` triggers the `prepare` script, which points git at the versioned hooks directory (`git config core.hooksPath .githooks`). From then on, every commit auto-fixes and validates any staged `*.md` files.

Useful scripts:

- `pnpm run lint:md` — lint every markdown file
- `pnpm run lint:md:fix` — auto-fix every markdown file

Rules live in [`../.markdownlint.json`](../.markdownlint.json) — tuned for prose-heavy learning content (e.g. line-length and code-fence-language checks are disabled).
