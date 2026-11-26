# Explain Clearly

[中文](README.md) | **English**

> An Agent Skill that helps AI agents explain technical knowledge clearly: concepts, mechanisms, failure causes, and design trade-offs.

Explain Clearly is not tied to any single agent. It uses the general Agent Skills format (`SKILL.md`: YAML frontmatter plus Markdown rules), so any agent that supports this format can load and use it, including Claude Code, Codex, and others. The `agents/openai.yaml` file in this repo is only UI metadata for Codex; other agents can safely ignore it.

## What problem it solves

Ask an AI a technical question directly and you often get an answer that is correct but not very useful: you get the conclusion, but not why it holds, where its boundaries are, or whether it still applies in a different scenario.

Explain Clearly makes the agent explain in a way that:

- **Starts from what you know**: it first judges what you already understand, skips the known parts, and fills in only the prerequisites that affect this question. It also distinguishes "can use it" from "understands how it works inside".
- **Uses one scenario throughout**: the same example, the same piece of code, or the same worked calculation carries all the key steps, instead of a different unrelated example for every concept.
- **Picks from five dimensions**: What (what it is), Why (why it is needed), How (how it works), When (when it applies), Compare (how it differs from alternatives) — chosen per question, never mechanically filled in.
- **Stays traceable and checkable**: observable objects and changes come before terminology; confirmed facts, teaching assumptions, and inferences are kept distinct; causal explanations state the conditions under which they hold and observations you can verify.
- **Handles follow-ups**: when something does not click, it locates the exact sticking point, fills just that gap, and adjusts the depth using the background already established.
- **Remembers your level**: the skill maintains an internal learning record in your workspace (`.explain-clearly/memory.md`), so later explanations continue from your current familiarity.

## Before / after

Take the question: "We use a database connection pool, so why do requests still wait?" The two answers below illustrate the difference in style.

**A short answer**

> When all connections in the pool are in use, new requests wait until a connection becomes available.

**Explained with Explain Clearly**

> Suppose the pool allows at most two connections. Requests A and B each borrow one, so when request C arrives it has to wait. After A finishes reading its results and returns its connection, C borrows that same connection and runs its query. If C times out before it gets a connection, it returns an error instead.

```text
A and B each hold a connection → C waits → A returns its connection → C borrows it and runs the query
```

## Installation

Clone this repository into your agent's skills directory, keeping the folder name `explain-clearly`:

| Agent | Skills directory |
| --- | --- |
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.codex/skills/` |
| Other agents supporting Agent Skills | The skills directory specified in their documentation |

macOS / Linux (Claude Code as the example):

```bash
git clone https://github.com/AW-one/Explain-clearly.git ~/.claude/skills/explain-clearly
```

Windows PowerShell (Claude Code as the example):

```powershell
git clone https://github.com/AW-one/Explain-clearly.git "$env:USERPROFILE/.claude/skills/explain-clearly"
```

For Codex, replace the path with `$env:USERPROFILE/.codex/skills/explain-clearly`; everything else is the same.

## Usage

This skill is invoked explicitly: name it before your question.

- Codex: type `$explain-clearly`, select **Explain Clearly**, then write your question.
- Claude Code: type `/explain-clearly`, then write your question.
- Other agents: simply say "explain ... using explain-clearly".

```text
$explain-clearly Explain how Go's context.Context works, using a request-cancellation scenario.
```

For follow-up questions you do not need to name it again; the skill continues in the same style.

## Repository layout

```text
SKILL.md             The skill rules themselves
references/          Five worked examples: concept / mechanism / causality / comparison / algorithm
agents/openai.yaml   UI metadata for Codex (optional; other agents can ignore it)
LICENSE              MIT License
```

Before a substantial explanation, the skill reads the most relevant example from `references/` for the question type, to calibrate how it explains. See [SKILL.md](SKILL.md) for the full rules and [references/](references/) for the examples.

## License

[MIT](LICENSE)
