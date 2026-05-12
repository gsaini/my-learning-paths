# 4. AI Coding Workflow 101

The **AI coding workflow** is the end-to-end loop developers use to ship production code with AI assistance. Tools like Claude Code, Cursor, Copilot, and Aider differ in autonomy level, but the underlying workflow is the same.

> Tip: AI coding is *not* "type a prompt, get a feature." It is a disciplined loop of **specifying, scoping, generating, verifying**.

---

## The Six-Step Loop

```
1. Spec  ─►  2. Context  ─►  3. Plan  ─►  4. Generate  ─►  5. Verify  ─►  6. Iterate
```

### 1. Spec — Define the outcome

- Write a short PRD or task description.
- Acceptance criteria: *what does "done" look like?*
- Constraints: language, framework, style, performance, security.

### 2. Context — Give the model what it needs

- Relevant files, schemas, types, examples.
- Project conventions (CLAUDE.md, READMEs, style guides).
- Failing test, error message, or screenshot if debugging.

### 3. Plan — Get alignment before code

- Ask the AI for a plan first; review it.
- Catch wrong assumptions cheaply, before generation.
- For big tasks, break into smaller atomic chunks.

### 4. Generate — Let the model code

- Small diffs > sweeping rewrites.
- Prefer edits to existing files over new files.
- Keep the AI on a single concern at a time.

### 5. Verify — Don't trust, test

- Run the type checker, linter, tests.
- Run the feature manually in a real environment (esp. UI).
- Read the diff line-by-line — don't merge what you haven't read.

### 6. Iterate — Feed errors back

- Paste failing test output → AI fixes.
- Tighten the spec when output drifts.
- Commit checkpoints frequently so you can revert.

---

## Levels of Autonomy

| Level | Style | Tools | When to use |
|-------|-------|-------|-------------|
| L1 — Autocomplete | Inline suggestions | Copilot, Tabnine | Boilerplate, syntactic help |
| L2 — Chat in IDE | Ask, edit, explain | Cursor chat, Continue | Targeted edits, code review |
| L3 — Multi-file edit | Diff across the repo | Cursor Composer, Aider | Refactors, feature additions |
| L4 — Agentic | Plans, edits, runs tests, iterates | Claude Code, Devin | Complex tasks with tight specs |
| L5 — Async/cloud | Spawns PRs without you watching | Claude Code routines, Devin | Bug triage, dependency bumps |

---

## Best Practices

- **Small, scoped tasks.** "Add a login page" beats "build auth."
- **One concern per chat** — long sessions accumulate confusion.
- **Read the code.** AI fluently produces wrong code. The diff is the truth.
- **Tests are your harness** — the AI uses them to know when it's done.
- **Commit checkpoints** before risky changes; `git reset` is your safety net.
- **Define conventions** in a CLAUDE.md so you don't repeat yourself.
- **Human-in-the-loop for risky ops** — DB migrations, prod deploys, secrets.

---

## Anti-Patterns

- "Vibe coding" without reading what was generated.
- Letting the agent run for 30 minutes unattended on a vague task.
- Mixing unrelated changes in one chat.
- Asking for a feature without showing the file structure.
- Skipping tests because "the AI said it works."

---

## A Reference Day-to-Day Loop (Claude Code style)

1. Open repo, brief context auto-loaded from CLAUDE.md.
2. Describe the task in 2–3 sentences.
3. Agent proposes a plan. You correct it.
4. Agent edits files, runs tests, shows diff.
5. You read the diff, ask for changes.
6. Commit when green. Repeat.

---

## Tooling Snapshot (2025–26)

- **Claude Code** — terminal + IDE agentic assistant.
- **Cursor** — VS Code fork with deep multi-file editing.
- **GitHub Copilot** — autocomplete + chat + workspace mode.
- **Aider** — terminal pair-programmer with git integration.
- **Devin / Cline / Replit Agent** — autonomous engineers.

---

## Further Reading

- Anthropic — *Claude Code best practices*
- "Beyond autocomplete" — agentic coding posts
- *The Pragmatic Programmer* (still relevant — same discipline applies)
