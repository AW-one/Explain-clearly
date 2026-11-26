# Explain Clearly

[中文](README.md) | **English**

> An Agent Skill that helps AI agents explain technical knowledge clearly: concepts, mechanisms, failure causes, and design trade-offs.

Explain Clearly is not tied to any single agent. It uses the general Agent Skills format (`SKILL.md`: YAML frontmatter plus Markdown rules), so any agent that supports this format can load and use it, including Claude Code, Codex, and others. The `agents/openai.yaml` file in this repo is only UI metadata for Codex; other agents can safely ignore it.

## What problem it solves

Ask an AI a technical question directly and you often get an answer that is correct but not very useful: you get the conclusion, but not why it holds, where its boundaries are, or whether it still applies in a different scenario.

Explain Clearly makes the agent explain in a way that:

- **Teaches completely by default**: the first time you ask about a concept or technology, it teaches systematically through five chapters — What, Why, How, When, Compare — instead of handing you a summary. It narrows the scope only when you explicitly ask for something brief or limited.
- **Offers three depth levels**: Quick (100–500 words, for short answers and follow-ups), Standard (1,500–2,500 words, the default for first-time learning), and Deep (3,000+ words, for complex algorithms, low-level principles, and system architecture). Length follows the difficulty of the question and your request.
- **Starts from what you know**: it first judges what you already understand, skips the known parts, and fills in only the prerequisites that affect this question. It also distinguishes "can use it" from "understands how it works inside".
- **Uses one scenario throughout**: one main case carries all five chapters. The agent tracks its objects, inputs, and state changes until a result appears, then varies the conditions to show boundaries and comparison conclusions.
- **Researches before it teaches**: in Standard and Deep, the agent first searches three directions — the latest versions and technical evolution, mainstream alternatives, and improvements and derived techniques — preferring official documentation, official repositories, and original papers, and distinguishing mature technology, experimental approaches, and research results.
- **Compares in three directions**: horizontally, the responsibilities and trade-offs of comparable alternatives; vertically, how important versions evolved; and derived techniques — what pain point each improvement solves, its core change, and its main limitations.
- **Visualizes on request**: by default it uses Markdown tables, Mermaid, or monospace text diagrams. Only when you explicitly ask for pictures or diagrams ("explain with images", "draw it") does it switch to the enhanced visual mode, retrieving real photos, official structure diagrams, or figures from papers, and generating images or interactive demos only when necessary.
- **Stays traceable and checkable**: each term is explained where it first appears, in terms of the case at hand; confirmed facts, teaching assumptions, and inferences are kept distinct; causal explanations state the conditions under which they hold and observations you can verify. Before answering, the agent self-checks coverage, depth, terminology, case continuity, applicability boundaries, and sources.
- **Handles follow-ups**: when something does not click, it locates the exact sticking point, fills just that gap, and adjusts the depth using the background already established.
- **Remembers your level**: the skill maintains an internal learning record in your workspace (`.explain-clearly/memory.md`), so later explanations continue from your current familiarity.

## Before / after

Take the question: "We use a database connection pool, so why do requests still wait?" The two answers below illustrate the difference in style. The excerpt only demonstrates the "one scenario throughout" approach; a default complete-teaching answer is much longer and unfolds systematically through the five chapters.

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

You can also steer the depth and form in plain words: "keep it brief" selects Quick, "go deep" selects Deep, and asking to "explain with images" or "draw it" enables the enhanced visual mode for that answer.

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
