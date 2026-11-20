# 第9章 检测与守卫：输入输出的关卡

**一句话**：`prompt guards` 与 `injection detector` 在输入与输出两侧扫描，是关卡，不是免疫系统。

**为什么需要它**：`prompt injection detection` 是独立于主模型的检测方向。`OWASP` 建议定义敏感类别、使用 `semantic filter` 与字符串检查，并用 `deterministic code` 验证输出格式。守卫的价值在于它不依赖主模型自觉——即使主模型被注入，外部关卡仍可能拦住动作。

**核心结构**：输入侧用 `input filtering` 与 `semantic filter` 扫描 `malicious instruction` 与 `adversarial prompt`；输出侧用 `output filtering` 检查是否越权、是否触发 `data exfiltration`；`prompt guards` 把两侧能力组合成可配置组件，`injection detector` 给出是否可疑的判定。与 `tool filters` 配合时，检测负责发现异常，过滤负责阻断执行。

**关键设计**：守卫本身也是模型或规则系统，因此同样会被 `adaptive attacks` 绕过——攻击者知道你在检测什么，就会朝检测盲区优化。综合报告口径明确指出，守卫应与权限控制叠加使用，而不是单独依赖。检测只能降低成功概率，不能消灭那条同通道的结构性问题。

**拆解**：
### 1. 输入侧：筛查可疑指令
`input filtering` 与 `semantic filter` 能挡住显式的注入句式，但面对措辞变换与编码混淆会漏。它的定位是「降低命中率」，不是「保证零命中」。

### 2. 输出侧：校验越权动作
`output filtering` 配合 `deterministic code` 检查格式与动作边界，能在动作发出前拦住明显越权的 `tool calls`。

### 3. 检测器自己的软肋
`adaptive attacks` 会针对已知守卫优化，`Attacker Moves Second` 的结论对检测器同样成立。守卫上线后必须定期用新攻击样本复测。

### 4. 叠加才有意义
检测失败时，`least privilege` 与 `sandbox` 必须还在。把守卫当最后一道防线，等于把系统安全押在一个同样会被攻击的模型上。

**今天怎么做**：把每次守卫判定——放行、拦截、可疑——连同输入来源一起写入日志，定期用红队样本复查误报与漏报；检测阈值变更要像模型变更一样做回归。

<div class="chapend" markdown="1">

**容易误解的地方**：以为检测器命中率高就等于安全。命中率是对固定样本的分布指标，对自适应攻击者不成立。

**记住这一句**：守卫是纵深的一层，不是终点；检测失败时还必须有权限兜底。

</div>
