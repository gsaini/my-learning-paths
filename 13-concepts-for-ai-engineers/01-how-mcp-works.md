# 1. How MCP Works

**Model Context Protocol (MCP)** is an open standard introduced by Anthropic in November 2024 that lets LLMs connect to external tools, data sources, and systems through a single uniform interface. It is often called the *"USB-C for AI"* because it replaces dozens of brittle custom integrations with one plug-and-play protocol.

---

## Why MCP Exists

Before MCP, every LLM-powered app had to implement bespoke connectors for each external system: one for Postgres, one for Slack, one for Google Drive, etc. This created an **N × M integration problem** — N AI apps × M tools.

MCP collapses this into **N + M**: each app implements one MCP client; each tool implements one MCP server. They speak the same language.

---

## Core Architecture

```
┌─────────────────┐        ┌──────────────┐        ┌─────────────────┐
│  Host (LLM app) │ <----> │  MCP Client  │ <----> │   MCP Server    │
│  e.g. Claude    │  JSON- │  (1 per      │  stdio │  (wraps a DB,   │
│  Desktop, IDE   │  RPC   │   server)    │  /HTTP │   API, FS, etc.)│
└─────────────────┘        └──────────────┘        └─────────────────┘
```

- **Host** — the application embedding the LLM (Claude Desktop, Cursor, custom agent).
- **Client** — lives inside the host, manages a 1:1 connection to one server.
- **Server** — a process exposing capabilities. Can be local (stdio) or remote (HTTP/SSE).
- **Transport** — JSON-RPC 2.0 over stdio or Streamable HTTP.

---

## The Three Primitives

| Primitive | Direction | Purpose | Example |
|-----------|-----------|---------|---------|
| **Tools** | Server → Model can invoke | Functions with side effects | `create_jira_ticket`, `run_sql` |
| **Resources** | Server → Host reads | Read-only data the model can reference | `file://README.md`, `db://schema` |
| **Prompts** | Server → User selects | Reusable, parameterized prompt templates | `/summarize-pr`, `/refactor` |

Newer additions: **Sampling** (server asks the host's LLM to generate something) and **Roots** (filesystem boundaries).

---

## A Typical Request Flow

1. Host starts → spawns MCP clients → connects to configured servers.
2. Each server returns its **capability manifest** (tools, resources, prompts).
3. The host injects tool schemas into the LLM's context.
4. User asks: *"What's in our customers table?"*
5. LLM emits a tool call: `query_db(sql="SELECT * FROM customers LIMIT 10")`.
6. Host routes the call to the right MCP server.
7. Server executes, returns rows.
8. Host appends the result to the conversation; the LLM answers.

---

## Security & Trust

- Servers run with **user-level permissions** — they can do anything the user can.
- Hosts should **prompt for consent** before invoking destructive tools.
- Use **scoped credentials**, not personal tokens, when possible.
- Audit the servers you install — they receive your prompts and data.

---

## Notable Real-World MCP Servers

- **Filesystem** — read/write local files
- **GitHub** — issues, PRs, code search
- **Slack** — read channels, send messages
- **Postgres / SQLite** — query databases
- **Puppeteer / Playwright** — control a browser
- **Brave Search / Google Drive / Notion** — knowledge access

---

## Strengths and Limitations

**Strengths**

- Single integration surface across hosts.
- Local-first by default → privacy-friendly.
- Open spec, multiple SDKs (Python, TS, Go, Rust, Java, C#).

**Limitations**

- Discoverability is still maturing (no central registry yet).
- Auth/identity for remote servers is evolving (OAuth in newer specs).
- Server quality varies — supply-chain risk on third-party servers.

---

## Further Reading

- modelcontextprotocol.io — official spec
- github.com/modelcontextprotocol/servers — reference servers
- Anthropic engineering blog posts on MCP
