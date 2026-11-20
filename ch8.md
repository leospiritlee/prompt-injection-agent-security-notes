# 第8章 工具权限：让被注入也无法动手

**一句话**：`least privilege`、`sandbox` 与 `tool filters` 把「模型被控制后还能做什么」压到最小，是任何方案都该叠加的底座。

**为什么需要它**：注入的终点通常是 `privilege escalation` 或 `data exfiltration`。`OWASP` 明确列出 `sensitive information` 泄露、`unauthorized access` 与执行 `arbitrary commands` 等后果。既然模型天然不可信，最关键的问题就不是它有没有被骗，而是它手上有什么。

**核心结构**：四件套按位置排列：`least privilege` 收窄 `credentials` 与 `access control`；`tool filters` 在调用前按 `security policies` 拦截高危操作；`sandbox` 与 `isolation` 把工具执行放进受控环境；`human in the loop` 对不可逆动作兜底。`OWASP` 特别强调把函数实现放在应用代码里，只给模型最小必要权限，而不是把高权限能力直接暴露出去。

**关键设计**：这件事的目标函数变了——从「让模型听话」变成「模型即使完全失控，损害半径也有上界」。`credentials` 的范围、`tool calls` 的准入、`sandbox` 的边界，三者共同决定这个上界。综合报告口径认为，在多类基准中 `tool filters` 与 `least privilege` 被反复验证为兼顾安全与可用性的高性价比防线。

**拆解**：
### 1. 凭据：按任务收窄
不要让 Agent 用一个全能凭据处理所有任务。按任务拆分 `credentials`，明确 `access control` 能读到的数据范围，能只读就不要可写。

### 2. 调用：前置拦截
`tool filters` 在 `tool calls` 发出之前按 `security policies` 判定，默认拒绝高危组合。守卫在这里是规则与代码，不是模型的自我约束。

### 3. 执行：限定爆炸半径
把工具放进 `sandbox` 与 `isolation` 中运行，让一次越权调用碰不到主进程与网络出口。这一步决定最坏情况的下限。

### 4. 兜底：不可逆动作要人确认
删除、付款、对外发送这类动作加 `human in the loop`。自动化的收益不该以不可逆为代价。

**今天怎么做**：给每个工具按「最坏后果」分级，最高级默认拒绝并加确认与隔离；用 `tool filters` 显式列出允许的组合，而不是列禁止项。综合报告口径建议从最小可用权限起步，按任务逐步放开。

<div class="chapend" markdown="1">

**容易误解的地方**：以为输入侧加了过滤就不需要权限控制。过滤可能被绕过，权限与沙箱是最后兜底。

**记住这一句**：权限是最后一道真防线；模型可以被骗，`credentials` 和 `sandbox` 不能跟着被骗。

</div>
