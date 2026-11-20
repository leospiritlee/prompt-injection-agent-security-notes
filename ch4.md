# 第4章 评测基准：怎么算一次攻击成功

**一句话**：`AgentDojo`、`InjecAgent`、`JailbreakBench` 与 `Agent Security Bench` 把「成功」变成一个可复核的双指标问题。

**为什么需要它**：`JailbreakBench` 摘要列出三个老大难：没有清晰的评测实践标准、各工作的成功率与成本算法不可比、不少工作不可复现——隐藏 `adversarial prompts`、代码闭源、依赖不断演进的专有 API。没有基准，防御效果就只能靠口号；有了基准，还要看它到底在量什么。

**核心结构**：`AgentDojo` 不是静态测试集，而是可扩展的 `evaluation framework`，论文给出 `97 realistic tasks` 与 `629 security test cases`，并把 `adaptive attacks` 当一等公民。`InjecAgent` 有 `1,054` 个测试用例，覆盖 17 类 `user tool` 与 62 类 `attacker tool`，专门量工具集成 Agent 的间接注入。`JailbreakBench` 维护 `evolving repository` 的对抗提示，强调 `reproducible` 与可比的 `success rates`。`Agent Security Bench` 覆盖十类场景、`over 400 tools` 与多类攻防，论文摘要报告最高平均 `attack success rate` 为 `84.30%`。

**关键设计**：几乎所有现代基准都同时报告 `attack success rate` 与 `utility`。这不是凑数——如果只报安全，那么「把所有工具禁用」就能拿满分，而这样的 Agent 没有任何用处。`Formalizing and Benchmarking` 用 `5 prompt injection attacks`、`10 defenses`、`10 LLMs` 与 `7 tasks` 做系统评测，目的也是给领域一个共同标尺。

**拆解**：
### 1. 先定义什么叫成功
越狱看是否产出有害内容；注入看模型是否执行了攻击者的任务；Agent 场景则看是否产生越权的 `tool calls` 或 `data exfiltration`。定义不同，数字不可横向比较。

### 2. 双指标：安全不能拿可用性换
`utility` 与 `attack success rate` 必须一起看。只看前者会把危险的方便当成成果，只看后者会把无能当成安全。综合报告口径反复强调这一点。

### 3. 可复现性决定结论是否算数
`evolving repository`、公开的 `adversarial prompts`、明确的 `cost` 计算方式，是评测能被信任的前提。`reproducible` 不是加分项，是底线。

### 4. 自适应攻击是试金石
`AgentDojo` 把 `adaptive attacks` 纳入框架，正是因为它会针对已知防御重新优化。固定 `test cases` 上的高分，放到自适应轮里往往大幅回落。

**今天怎么做**：先跑一个现成 `benchmark` 拿基线，同时记录 `attack success rate` 与 `utility`；再补一小组针对自己防御的自适应用例，看分数怎么变。两次都要留 `reproducible` 的配置与日志。

<div class="chapend" markdown="1">

**容易误解的地方**：以为榜单分数能直接迁到自己的业务。换任务、换工具集、换数据分布，攻击面就变了，基准只是起点。

**记住这一句**：没有 `utility` 的安全分数没有意义；没有 `adaptive attacks` 的评测会高估防御。

</div>
