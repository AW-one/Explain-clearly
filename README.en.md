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

## A real case: same question, two answers

Same question, same agent (Codex), one single difference: in one run `$explain-clearly` was invoked explicitly, in the other the question was asked directly. The question in both runs was "Explain what an Agent Skill is, in detail."

| Aspect | With Explain Clearly | Without the skill |
| --- | --- | --- |
| Structure | Complete teaching in five chapters: what it is, why make it a skill, how it works, when it is worth using, and how it relates to prompts and project rules | Three parts: definition, what a skill file looks like, how a request is executed |
| Teaching material | Explains using the asker's own explain-clearly skill and the actual flow of that very session | Uses an invented review-changes example |
| Research and sources | Checked the open specification, official documentation, and research; key claims carry sources | Cites only a few sources, such as the open specification |
| Extra coverage | Invocation policy (explicit vs. implicit), how to evaluate skills, cross-platform evolution, SkillsBench benchmark data | Not covered |
| Length | Long, roughly three times the other answer | Short and quicker to read |

The difference is not correctness — both answers are right — but completeness and fit: the skill turns "in detail" into a fixed teaching scope, research requirements, and acceptance checks. The two full answers are below, click to expand. They are reproduced in Chinese, as generated; links to local files in the originals were removed and heading levels adjusted for layout.

<details>
<summary>Answer generated with Explain Clearly (full text, in Chinese)</summary>

**Agent Skill（智能体技能）是一套可以被 Agent 按需加载的任务做法。** 它把「这类任务该怎么做、需要参考什么、做到什么程度才算完成」写成可复用的说明。你刚才调用的 `$explain-clearly` 就是一个例子。你的提问决定**讲什么**，这个技能规定**怎么讲**，Codex 负责读取说明、使用工具并完成回答。

### 1. 它是什么？一个技能里装了什么？

按 Agent Skills 开放规范，一个技能是一个目录，核心文件叫 `SKILL.md`。这个文件分为两部分。开头用 `---` 包起来的 YAML 字段是**元数据**，其中 `name` 是技能名，`description` 描述它做什么、什么时候适用。后面的 Markdown 正文是**任务说明**，可以写操作步骤、判断条件、输出格式和验收标准。目录里还可以放参考材料 `references/`、可执行脚本 `scripts/` 和模板等资源 `assets/`。[Agent Skills 开放规范](https://agentskills.io/specification)

以你的 `explain-clearly` 为例，正文规定首次讲解通常要覆盖概念、原因、机制、适用条件和比较，还规定了遇到概念题、机制题或算法题时该选哪类范例。范例正文单独存放在 `references/` 里。它封装的是一套**教学方法**，所以换成连接池、数据库事务或注意力机制，同一个技能依然能用。

### 2. 为什么要把方法做成技能？

假设你经常问技术问题，每次只说「详细一点」。模型需要自行判断「详细」意味着什么。有时它会补很多背景，有时会直接跳到实现，结果可能省掉你最想知道的因果链。你当然可以每次把要求写进提问里，但只要这套要求经常复用，就会重复填写，也很难统一更新。把它存成技能后，你下一次只需给出新主题，讲解方法和验收要求就能一起复用。

这种做法还有一个运行上的好处。**上下文**是模型本轮能看到并用来工作的内容。一个 Agent 即使装了几十个技能，当前任务可能只用到一个。先提供名称和简介，选中后再读正文，需要时再读范例，就能把上下文留给当前任务。规范把这种加载方式称为**渐进式披露**。[Agent Skills 集成指南](https://agentskills.io/client-implementation/adding-skills-support)

### 3. 它是怎么工作的？看本轮实际走过的流程

1. **发现技能。** 支持 Skills 的客户端从它认可的位置发现技能，把名称、描述等信息提供给模型。此时已有足够信息判断技能是否相关，详细步骤等到启用时再读。[OpenAI 技能指南](https://learn.chatgpt.com/docs/build-skills)
2. **选择技能。** 用户可以显式点名。允许隐式调用时，模型也可以根据任务和 `description` 的匹配程度自行选用。你的 `explain-clearly` 在 Codex 配置里设置了 `allow_implicit_invocation: false`，所以这次由你点名启用。假如只发「解释一下 Agent Skill」，这份配置会关闭该技能的自动选择，Codex 仍然可以直接回答问题。（本地调用配置）· [OpenAI 调用策略](https://learn.chatgpt.com/docs/build-skills)
3. **加载说明，再按需读取资源。** 我把这题判断为概念题，读取了 `01-concept-pgxpool.md`。它示范了怎么先解释一个组件在系统里的位置，再沿实际过程讲清各环节的职责。这个技能也要求完整讲解核实相关资料，本轮因此查阅了开放规范、产品文档和研究。
4. **执行并产出结果。** Agent 结合你的问题、技能说明和工具返回的信息生成回答。带脚本的技能也是同样的过程，只是 Agent 还可以调用脚本完成某个步骤，例如校验表格列名。脚本能否运行、文件能否访问，取决于当前环境提供的工具和权限。[Agent Skills 资源规范](https://agentskills.io/specification#optional-directories) · [OpenAI 的技能与工具说明](https://developers.openai.com/plugins/concepts/skills)

这里有两个独立的质量问题：**有没有选中技能**，以及**选中以后有没有执行好**。描述太宽泛，容易在相邻任务上误触发。正文遗漏了关键判断条件，即使每次都能触发，结果也会有偏差。因此，评估时可以分别检查调用行为和最终产出。[OpenAI 技能评估指南](https://developers.openai.com/blog/eval-skills)

### 4. 什么时候值得用？

技能适合**反复出现、方法相对稳定、每次输入不同**的任务，比如技术讲解、审查 PR、按模板写发布说明。一次性要求「请用一个 Go 例子解释」直接写进提示词就够了。跨多个项目都会用到的教学偏好适合个人技能，团队特有的流程则可以跟仓库一起维护。收益来自少重复说明，代价是要维护正文、范例和依赖，让它们跟实际流程保持一致。[OpenAI 技能指南](https://learn.chatgpt.com/docs/build-skills)

还要看任务要求多严格。像「根据读者基础选一个合适的例子」适合交给模型判断。像「发布包必须通过格式校验才能发布」则需要由脚本或 CI 在执行处强制检查。技能可以指导 Agent 何时运行检查、如何解释失败，程序负责给出可复验的通过或失败结果。[OpenAI 技能编写指南](https://learn.chatgpt.com/docs/build-skills)

### 5. 它和提示词、项目规则、工具有什么关系？

| 机制 | 在实际任务里负责什么 |
| --- | --- |
| **本轮提示词** | 指定这一次的目标，比如「解释 Agent Skill，详细一点」。 |
| **Skill** | 提供一类任务的可复用做法，比如 `explain-clearly` 的选例、查证和讲解步骤。 |
| **`AGENTS.md`** | 提供工作区适用的项目规则。比如 Multica 的 `AGENTS.md` 规定修改数据库查询后如何重新生成 sqlc 代码。 |
| **Tool / MCP** | 工具执行具体动作，比如读取文件或查询 GitHub。MCP 是把外部数据和工具接入 Agent 的协议，技能可以指导 Agent 何时、按什么顺序使用它们。 |
| **Plugin** | 把一个或多个技能与连接器等资源打包，供别人安装和使用。 |

（Multica 项目规则）· [OpenAI 的 AGENTS.md 指南](https://learn.chatgpt.com/docs/agent-configuration/agents-md) · [OpenAI 的技能与 MCP 说明](https://developers.openai.com/plugins/concepts/skills) · [插件打包指南](https://developers.openai.com/plugins/build/plugins)

**为什么有些技能能跨平台，有些需要改？** Anthropic 在 2025 年 10 月推出 Agent Skills，12 月发布开放规范，2026 年 10 月将标准捐赠给 Agentic AI Foundation。开放规范统一了目录、说明文件和基本加载思路。各客户端仍有自己的安装位置和扩展字段。比如你的 `agents/openai.yaml` 是 Codex 的调用策略配置，移植时要在目标客户端重新确认显式调用和自动调用的设置。[Anthropic 的标准演进公告](https://www.anthropic.com/news/donating-agent-skills-to-the-agentic-ai-foundation) · [Agent Skills 集成指南](https://agentskills.io/client-implementation/adding-skills-support)

**它能让 Agent 做得更好吗？** SkillsBench 预印本在 87 项任务、18 种模型与运行环境组合上，对比了同一任务有无人工整理技能的结果。平均通过率从 **33.9% 提升到 50.5%**，增幅是 **16.6 个百分点**。这是特定任务和技能的实验结果。回到 `explain-clearly`，可以用同一组问题做类似比较，观察调用后是否更常讲完整机制、选对例子，并给出可核实的来源。[SkillsBench 预印本](https://arxiv.org/abs/2602.12670v4)

</details>

<details>
<summary>Answer generated without the skill (full text, in Chinese)</summary>

**Agent Skill 是给 AI 助手的一份可复用工作说明。** 它把一类任务的触发条件、操作步骤和交付要求放在一个目录里。比如，你每次审查代码都希望助手先弄清改动目的，再检查回归风险，最后给出带文件和行号的问题清单。把这套流程写成 Skill，以后只要说「审查这次改动」，助手就可以按同一套流程工作。[Agent Skills 开放规范](https://agentskills.io/specification)

### 一个 Skill 长什么样

最小版本只需要一个文件，比如 `review-changes/SKILL.md`：

```markdown
---
name: review-changes
description: 审查 Git diff 或拉取请求，找出会导致错误或破坏现有行为的改动。
---

1. 阅读改动及相关调用代码，确定哪些行为受影响。
2. 对每个问题，给出触发条件、实际影响和文件行号。
3. 按严重程度排列问题，并注明哪些行为尚未验证。
```

文件顶部的 **`name` 和 `description`** 帮助助手判断什么时候该用这个 Skill。下面的**正文**告诉它选中后怎么做。目录里还可以放 `references/` 存团队规范、`assets/` 存报告模板、`scripts/` 存可重复运行的检查脚本。纯文字流程也可以独立使用。[Agent Skills 文件格式](https://agentskills.io/specification)

### 从你发出请求到助手执行

1. **发现。** Pi 和 Codex 会先让模型看到可用 Skill 的名称、描述和路径。此时模型只需要知道有哪些流程可选。
2. **加载。** 当你说「帮我审查这次改动」，模型可以根据描述选中 `review-changes`，再读完整的 `SKILL.md`。自动选择可能漏掉，所以也可以直接点名。
3. **执行。** 助手按正文查看 Git diff、读相关代码，按需打开参考文件或运行脚本，然后按要求输出。实际操作由当前环境提供的工具执行，并受其权限约束。

这种**按需加载**让助手可以发现很多 Skill，同时只把当前任务需要的详细说明放进上下文。显式调用的语法由产品决定。Pi 用 `/skill:review-changes`，Codex CLI 可以用 `$review-changes`。两者都支持从项目的 `.agents/skills/` 发现技能。（Pi 技能文档）· [Codex 技能文档](https://learn.chatgpt.com/docs/build-skills#how-chatgpt-and-codex-use-skills)

在这个例子里，你的**提示词**指定审查哪次改动，`AGENTS.md` 提供仓库的长期规则，**Skill** 提供代码审查的具体流程，**Git 工具**提供实际的改动内容。Skill 最值得写的部分，是那些你会在同类任务里反复交代、又会影响结果的细节，比如「每条发现都要说明在什么条件下会出错」。

</details>

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

### Or: let your AI install it in one sentence

If you would rather not run commands yourself, send this sentence to your agent — it will find its own skills directory and clone the repo for you:

```text
Please install the Explain Clearly skill for me: clone https://github.com/AW-one/Explain-clearly into your skills directory, keeping the folder name explain-clearly; if the skills directory does not exist yet, create it first. When you are done, tell me how to invoke this skill.
```

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
