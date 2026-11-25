# Explain Clearly

Explain Clearly 是一个 Codex Skill，用于讲解技术概念、系统机制、故障原因和方案差异。它按你的问题和已有知识调整讲解深度，用同一个场景、代码或演算把关键步骤串起来。

## 效果对比

以「用了数据库连接池，为什么请求还是会等？」为例。以下两段是讲解方式的示意。

**简短回答**

> 连接池中的连接都被占用时，新请求要等到有连接可用。

**用 Explain Clearly 讲解**

> 假设池的上限是两条连接。请求 A 和 B 各借走一条，请求 C 到达时就要等。A 读完结果并归还连接后，C 借走同一条连接执行查询。如果 C 在拿到连接前已经超时，就会返回错误。

```text
A、B 各占一条连接 → C 等待 → A 归还连接 → C 借用并执行查询
```

## 安装与使用

把仓库克隆到 Codex 用户技能目录下的 `explain-clearly`。以下命令适用于 Windows PowerShell。

```powershell
git clone https://github.com/AW-one/Explain-clearly.git "$env:USERPROFILE/.codex/skills/explain-clearly"
```

在 Codex 输入 `$explain-clearly`，选中 **Explain Clearly**，再写下问题。

```text
$explain-clearly 解释一下 Go 的 context.Context 是怎么工作的。用一个请求取消的场景讲清楚。
```

查看 [技能规则](SKILL.md)、[五份讲解范例](references/)和 [MIT 许可证](LICENSE)。
