# 第3章 攻击面：越狱、对抗与多轮

**一句话**：`jailbreak` 与 `prompt injection` 相关但不等同；`adversarial prompt`、`GCG` 与 `many-shot jailbreaking` 让绕过从单句措辞变成可优化、可迁移、可累积的过程。

**为什么需要它**：`OWASP` 指出，越狱可视为提示注入的一种——攻击者使模型完全放弃安全协议；有效防御需要持续更新模型训练与安全机制，而不只是系统提示层的约束。综合报告口径强调，近年出现的 `many-shot jailbreaking` 表明，在上下文里堆砌大量示例会逐步削弱模型的安全拒答行为；`Attacker Moves Second` 则强调，针对已知防御的 `adaptive attacks` 往往能绕过静态防护。

**核心结构**：攻击形态可以看成一条升级线：措辞变换（`Ignore Previous Prompt`）→ 可优化后缀（`adversarial suffix`、`GCG`、`universal and transferable`）→ 多轮累积（many-shot）→ 自适应（`adaptive attacks`）→ 表示层抑制（`circuit breakers`）。`JailbreakBench` 与 `StrongREJECT` 处理的是这条线的元问题：怎么判断一次攻击算成功、怎么让评分可靠。

**关键设计**：`GCG` 这类工作展示的 `adversarial suffix` 能在不同模型间保持效果，`transferable` 说明优化出来的对抗提示有跨模型泛化能力。这意味着「针对我们这个模型测过没事」不能推出「换个模型也没事」，更不能推出「面对自适应攻击者也没事」。

**拆解**：
### 1. 措辞变换：组合空间才是难点
角色扮演、编码混淆、语气改写，单独一项都很朴素，但排列组合几乎无穷。把防御押在关键词表上，等于拿有限规则去追无限生成。论文把这些手法系统整理出来，正是为了说明「枚举式防御」的边界。

### 2. 可优化后缀：把绕过变成搜索
`GCG` 与 `universal and transferable` 工作把对抗提示变成可优化对象。一旦能搜索，攻击者就能针对固定防御找特定后缀；`transferable` 又让这份劳动可以跨模型复用。静态防御因此天然处于下风。

### 3. 多轮累积：单次不越界，累积越界
`many-shot jailbreaking` 不靠一句狠话，而是在上下文里逐步铺垫。单看每一轮都在边界内，连起来却改变了模型对「该不该拒答」的判断。这提示我们：只看单轮输入的守卫会漏掉整类风险。

### 4. 自适应与表示层：攻防同时升级
`adaptive attacks` 专门研究已知防御的绕法，`Attacker Moves Second` 把这件事推到极致；`circuit breakers` 则换思路，不改提示，而是在表示层识别并抑制有害计算路径。一个把评测标准抬高，一个把防御位置后移。

**今天怎么做**：红队用例至少分两批——固定集做基线，自适应集做压力测试；两批都记录 `success rates` 与 `cost`，别只看「有没有被攻破」一个布尔值。

<div class="chapend" markdown="1">

**容易误解的地方**：以为越狱和提示注入是同一件事。它们重叠，但越狱瞄准模型的安全协议，注入瞄准应用赋予模型的任务与权限。

**记住这一句**：攻击会适应防御；只用固定用例测出来的 `robustness` 不可信。

</div>
