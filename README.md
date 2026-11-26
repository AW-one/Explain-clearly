# Explain Clearly

> An Agent Skill that helps AI agents explain technical knowledge clearly: concepts, mechanisms, failure causes, and design trade-offs.
>
> 一个让 AI Agent 把技术知识讲清楚的技能：讲解概念、机制、故障原因和方案差异。

Explain Clearly 不是某个 Agent 的专属插件。它采用通用的 Agent Skills 格式（`SKILL.md`：YAML 头加 Markdown 规则），任何支持这一格式的 Agent 都能加载使用，包括 Claude Code、Codex 等。仓库里的 `agents/openai.yaml` 只是给 Codex 用的界面元数据，其他 Agent 可以忽略，不影响使用。

## 它解决什么问题

直接问 AI 一个技术问题，常常得到一句正确但没用的回答：结论有了，但你不知道它为什么成立、边界在哪、换个场景还对不对。

Explain Clearly 让 Agent 在讲解时做到：

- **按你的起点讲**：先判断你已经会什么，跳过已知部分，只补影响理解的前提；区分「会用」和「理解内部机制」。
- **一个场景贯穿**：用同一个例子、同一段代码或同一次演算把关键步骤串起来，而不是每个概念各举一个无关的例子。
- **五个维度选内容**：What（是什么）、Why（为什么需要）、How（怎么运作）、When（何时适用）、Compare（与替代方案的差异），按问题取舍，不机械凑齐。
- **可跟踪、可复查**：先讲能观察到的对象和变化，再引入术语；区分已确认的事实、教学假设和推断；讲因果时给出成立条件和可验证的观测。
- **承接追问**：没听懂时定位卡点，只补缺的那一环，并沿用已经讲清的背景调整深度。
- **记住你的水平**：技能会在工作区维护一份内部学习记录（`.explain-clearly/memory.md`），之后的讲解接着你的熟悉度往下讲。

## 效果对比

以「用了数据库连接池，为什么请求还是会等？」为例。以下两段是讲解方式的示意。

**简短回答**

> 连接池中的连接都被占用时，新请求要等到有连接可用。

**用 Explain Clearly 讲解**

> 假设池的上限是两条连接。请求 A 和 B 各借走一条，请求 C 到达时就要等。A 读完结果并归还连接后，C 借走同一条连接执行查询。如果 C 在拿到连接前已经超时，就会返回错误。

```text
A、B 各占一条连接 → C 等待 → A 归还连接 → C 借用并执行查询
```

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

## 使用

这个技能是显式调用的，在问题前点名它：

- Codex：输入 `$explain-clearly`，选中 Explain Clearly，再写下问题。
- Claude Code：输入 `/explain-clearly`，再写下问题。
- 其他 Agent：直接说「用 explain-clearly 讲一下……」。

```text
$explain-clearly 解释一下 Go 的 context.Context 是怎么工作的。用一个请求取消的场景讲清楚。
```

追问时不用重复点名，技能会沿用同一套讲法承接下去。

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
