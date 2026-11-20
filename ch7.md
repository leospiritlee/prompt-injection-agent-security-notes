# 第7章 结构与训练防御：把改动落到模型与执行路径

**一句话**：`StruQ` 用 `structured queries` 把提示与数据分到 `two channels`，`SecAlign` 用 `preference optimization` 塑形安全偏好，`CaMeL` 把执行交给外部 `interpreter`。

**为什么需要它**：既然提示层的天花板明显，研究就转向两处——改模型看到的结构，或改模型与工具之间的执行契约。三条路线分别回答同一个问题的不同层次：怎么让数据不被当命令、怎么让模型偏好安全响应、怎么让不可信数据影响不了程序流。

**核心结构**：`StruQ` 的系统由 `secure front-end` 把提示与数据格式化进不同字段，训练模型只从指令通道取命令；`SecAlign` 用 `preference optimization` 让模型偏好安全响应；`CaMeL` 显式抽取 `control and data flows`，用 `capability` 与 `security policies` 在工具调用时执行策略，由 `interpreter` 跑程序。论文摘要报告 `CaMeL` 在部分基准上 `solving 77%` 的任务，同时挡住注入——保证来自结构，不是来自模型更听话。

**关键设计**：`CaMeL` 的关键判断是：即使底层模型可被攻破，只要不可信数据无法改变 `control flow`，攻击者就调不动越权工具、外泄不了 `private data`。代价是通用性下降，复杂任务需要更细的 `policy` 设计。综合报告口径认为它适合高安全要求的封闭域场景。

**拆解**：
### 1. 改结构：两条通道
`structured queries` 把「命令」与「待处理的数据」放进不同字段，模型不必从一段混合文本里猜哪句是命令。这是接口层的修复，改造成本中等。

### 2. 改偏好：安全响应更被偏好
`SecAlign` 用 `preference optimization` 训练模型偏好安全响应，从对齐角度降低注入成功率。它改的是行为倾向，仍要和其他层叠加。

### 3. 改执行：控制流外置
`CaMeL` 把 `control flow` 交给 `deterministic code`，用 `capability` 约束数据流向。这一步把安全保证从「模型别犯错」挪到「程序不允许」，是强度最高的一类。

### 4. 代价：保证与灵活性的交换
越强的设计层方案，对任务的限制越多。选型时要看你的任务是否足够结构化、策略能否写清，而不是只看论文里的成功数字。

**今天怎么做**：高风险场景先评估 `CaMeL` 式设计能覆盖多少任务——把典型任务拆成 `tool calls` 序列，看能否用 `security policies` 表达。能表达就优先用结构保证，不能表达再退回训练层加固。

<div class="chapend" markdown="1">

**容易误解的地方**：以为训练层防御等于彻底免疫。`SecAlign` 与 `instruction hierarchy` 提升的是概率与稳健性，不是数学意义上的不可攻破。

**记住这一句**：结构决定上限；把执行权交给 `deterministic code`，比指望模型每次都不犯错更可靠。

</div>
