# Explain Clearly

**中文** | [English](README.en.md)

> An Agent Skill that helps AI agents explain technical knowledge clearly: concepts, mechanisms, failure causes, and design trade-offs.
>
> 一个让 AI Agent 把技术知识讲清楚的技能：讲解概念、机制、故障原因和方案差异。

Explain Clearly 不是某个 Agent 的专属插件。它采用通用的 Agent Skills 格式（`SKILL.md`：YAML 头加 Markdown 规则），任何支持这一格式的 Agent 都能加载使用，包括 Claude Code、Codex 等。仓库里的 `agents/openai.yaml` 只是给 Codex 用的界面元数据，其他 Agent 可以忽略，不影响使用。

## 它解决什么问题

直接问 AI 一个技术问题，常常得到一句正确但没用的回答：结论有了，但你不知道它为什么成立、边界在哪、换个场景还对不对。

Explain Clearly 让 Agent 在讲解时做到：

- **默认完整教学**：第一次问某个概念或技术，默认按 What、Why、How、When、Compare 五章系统讲透，而不是给一段摘要；只有你明确说「简单讲讲」或限定范围时才收窄。
- **三档深度**：Quick（100–500 字，简短问答和承接式追问）、Standard（1500–2500 字，首次学习的默认档）、Deep（3000 字以上，复杂算法、底层原理、系统架构），篇幅跟着问题难度和你的要求走。
- **按你的起点讲**：先判断你已经会什么，跳过已知部分，只补影响理解的前置知识；区分「会用」和「理解内部机制」。
- **一个场景贯穿**：用同一个主案例串起五章，跟踪其中的对象、输入和状态变化直到出现结果，再用条件变化说明边界和对比结论。
- **先检索，再讲解**：Standard 和 Deep 在作答前会主动检索三个方向——最新版本与技术演进、同类与替代方案、改进方法与衍生技术，优先官方文档、官方仓库和原始论文，并区分成熟技术、实验性方案和研究成果。
- **Compare 讲三个方向**：横向比同类方案的职责与取舍，纵向讲重要版本演进，衍生技术讲改进方法解决了什么痛点、核心改进和主要局限。
- **可视化按需开启**：默认用表格、Mermaid 或等宽文本示意图；你明确说「配图讲」「画图解释」时才启用增强可视化，检索实物图片、官方结构图或论文原图，必要时才生成图片或交互式演示。
- **可跟踪、可复查**：术语在首次出现时结合案例解释；区分已确认的事实、教学假设和推断；讲因果时给出成立条件和可验证的观测。输出前按知识覆盖、深度、术语、案例连续性、应用边界和来源逐项自检。
- **承接追问**：没听懂时定位卡点，只补缺的那一环，并沿用已经讲清的背景调整深度。
- **记住你的水平**：技能会在工作区维护一份内部学习记录（`.explain-clearly/memory.md`），之后的讲解接着你的熟悉度往下讲。

## 效果对比

以「用了数据库连接池，为什么请求还是会等？」为例。以下两段是讲解方式的示意，节选只演示「同一场景贯穿」的讲法；默认的完整教学会比这长得多，按五章系统展开。

**简短回答**

> 连接池中的连接都被占用时，新请求要等到有连接可用。

**用 Explain Clearly 讲解**

> 假设池的上限是两条连接。请求 A 和 B 各借走一条，请求 C 到达时就要等。A 读完结果并归还连接后，C 借走同一条连接执行查询。如果 C 在拿到连接前已经超时，就会返回错误。

```text
A、B 各占一条连接 → C 等待 → A 归还连接 → C 借用并执行查询
```

## 真实案例：同一个问题，两种回答

同一个问题、同一个 Agent（Codex），只差一个变量：一次显式调用 `$explain-clearly`，一次直接提问。问题都是「解释一下什么是 Agent Skill，详细一点」。

| 维度 | 用 Explain Clearly | 不用 skill |
| --- | --- | --- |
| 结构 | 五章完整教学：是什么、为什么做成技能、工作流程、何时值得用、与提示词和项目规则的关系 | 定义、文件长相、工作流程三段 |
| 讲解对象 | 结合提问者自己的 explain-clearly 和本轮真实走过的流程讲 | 用一个虚构的 review-changes 例子讲 |
| 查证与来源 | 查阅开放规范、官方文档和研究，关键结论带来源 | 只引用了开放规范等少量来源 |
| 额外覆盖 | 调用策略（显式 / 隐式）、技能评估方法、跨平台演进、SkillsBench 基准数据 | 未涉及 |
| 篇幅 | 长，约是右边的三倍 | 短，读完更快 |

差别不在对错——两份都讲对了——而在完整度和贴合度：skill 把「详细一点」落实成了固定的教学范围、查证要求和验收标准。两份回答全文如下，点击展开查看（收录时去掉了原文里指向本地文件的链接，标题层级为排版略作调整）。

<details>
<summary>使用 Explain Clearly 的回答（全文）</summary>

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
<summary>未使用 skill 的回答（全文）</summary>

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

## 安装

把仓库克隆到你的 Agent 的技能目录下，目录名保持 `explain-clearly`：

| Agent | 技能目录 |
| --- | --- |
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.codex/skills/` |
| 其他支持 Agent Skills 的 Agent | 见其文档指定的技能目录 |

macOS / Linux（以 Claude Code 为例）：

```bash
git clone https://github.com/AW-one/Explain-clearly.git ~/.claude/skills/explain-clearly
```

Windows PowerShell（以 Claude Code 为例）：

```powershell
git clone https://github.com/AW-one/Explain-clearly.git "$env:USERPROFILE/.claude/skills/explain-clearly"
```

Codex 用户把路径换成 `$env:USERPROFILE/.codex/skills/explain-clearly` 即可，其余相同。

### 或者：让 AI 一句话装好

不想自己敲命令的，把下面这句话直接发给你的 Agent，它会自己找到技能目录并克隆好：

```text
请帮我安装 Explain Clearly 这个 skill：把 https://github.com/AW-one/Explain-clearly 克隆到你的技能目录下，目录名保持 explain-clearly；如果技能目录还不存在，就先创建它。装好后告诉我怎么调用这个 skill。
```

## 使用

这个技能是显式调用的，在问题前点名它：

- Codex：输入 `$explain-clearly`，选中 Explain Clearly，再写下问题。
- Claude Code：输入 `/explain-clearly`，再写下问题。
- 其他 Agent：直接说「用 explain-clearly 讲一下……」。

```text
$explain-clearly 解释一下 Go 的 context.Context 是怎么工作的。用一个请求取消的场景讲清楚。
```

追问时不用重复点名，技能会沿用同一套讲法承接下去。

深度和形式也可以随口控制：说「简单讲讲」走 Quick，「深入讲讲」走 Deep；说「配上图片解释」「画图讲」会开启增强可视化模式，只对当前回答生效。

## 仓库结构

```text
SKILL.md             技能规则本体
references/          五份讲解范例：概念 / 机制 / 因果 / 对比 / 算法
agents/openai.yaml   Codex 的界面元数据（可选，其他 Agent 可忽略）
LICENSE              MIT 许可证
```

实质性讲解前，技能会按问题类型选读一份最相关的范例来校准讲法。规则细节见 [SKILL.md](SKILL.md)，范例见 [references/](references/)。

## 许可证

[MIT](LICENSE)
