# Agent 自迭代领域综述

> 版本：v0.1  
> 日期：2026-06-18

## 1. 核心定义

Agent 自迭代不是让模型无约束地“自己变聪明”，而是在可观测、可评估、可回滚、可治理的边界内，把任务执行反馈转化为长期能力资产。

这些资产包括：

- prompt 和任务策略。
- 失败反思和经验记忆。
- 可复用技能、工具封装和 workflow。
- 自动评估器、回归测试和 benchmark。
- Agent runtime、代码脚手架和安全策略。

因此，当前更准确的说法是：

> 固定或半固定模型 + 可变 Agent 系统的工程化自进化。

## 2. 自迭代对象

| 层级 | 迭代对象 | 代表形式 |
| --- | --- | --- |
| Prompt 层 | 指令、反思、计划模板 | Reflexion、ReAct retry |
| Memory 层 | 失败经验、成功轨迹、用户偏好、环境知识 | episodic memory、semantic memory |
| Skill 层 | 可执行技能和任务片段 | Voyager skill library |
| Tool 层 | 工具 schema、调用策略、错误恢复 | tool router、tool verifier |
| Workflow 层 | 任务分解、handoff、并行策略 | agent graph optimization |
| Code 层 | Agent 脚手架、优化器、执行器 | STOP、AlphaEvolve 类系统 |
| Evaluator 层 | 打分器、judge、reward signal | self-rewarding、agent-as-judge |
| Model 层 | 通过 SFT / DPO / RL 更新参数 | SCoRe、Self-Rewarding LM |

目前工程上最容易落地的是 Prompt、Memory、Skill 和 Code with evaluator 四条线。Model 层自迭代潜力更大，但训练成本、评估偏差和安全边界也更复杂。

## 3. 主要技术路线

### 3.1 反思式自迭代

反思式路线让 Agent 在失败后生成语言化反思，将其写入短期或长期记忆，后续任务再读取这些经验。

典型循环：

```text
执行任务 -> 得到反馈 -> 反思失败原因 -> 写入记忆 -> 下次改进策略
```

优点：

- 实现简单，不需要训练模型。
- 适合低成本 retry 和长任务纠错。
- 能快速接入代码修复、Web task、问答推理等场景。

局限：

- 反思内容可能空泛。
- 容易把偶然经验写成长期规则。
- 对没有明确反馈的任务帮助有限。

### 3.2 技能库式自迭代

技能库路线把成功经验沉淀为可执行资产，而不是只保存自然语言记忆。典型做法是让 Agent 在完成任务后生成可复用函数、脚本、操作序列或 workflow，并通过验证后入库。

典型循环：

```text
探索任务 -> 生成技能 -> 执行验证 -> 修复错误 -> 入库 -> 组合复用
```

这条路线对企业 Agent 很重要，因为真实场景中的长期价值往往来自：

- 自动沉淀 SOP。
- 复用成功操作路径。
- 将人工经验转化为可调用工具。
- 把一次性任务变成可重复 workflow。

### 3.3 代码和算法搜索式自迭代

代码自迭代把 Agent 看作“候选生成器 + 自动评估器 + 搜索过程”。Agent 生成代码或算法变体，然后在沙箱中执行，按客观指标筛选，再继续变异或组合。

典型循环：

```text
LLM 生成候选 -> Sandbox 执行 -> 自动打分 -> 选择优秀变体 -> 再变异/组合
```

这条路线的优势在于评估信号相对客观，例如：

- 单元测试是否通过。
- benchmark 分数是否提升。
- 运行时延迟是否下降。
- 资源消耗是否减少。
- 代码 diff 是否满足约束。

核心前提是必须有可靠 evaluator。没有客观评分函数，代码自迭代很容易退化为语言自评。

### 3.4 自我奖励和自我评估

自我奖励路线让模型或 Agent 同时扮演生成者和评估者，通过 judge、reward model 或偏好数据构造训练信号。

典型循环：

```text
生成答案 -> 自评/互评 -> 构造偏好数据 -> DPO/RL 训练 -> 模型更新
```

潜力：

- 减少人工标注依赖。
- 可以扩展到主观任务。
- 有机会同时提升生成能力和评价能力。

风险：

- judge 偏差会被放大。
- 容易出现 reward hacking。
- 同源模型互评可能互相确认错误。
- 需要外部基准或人工抽检校准。

### 3.5 训练模型的自纠错能力

单纯提示模型“再检查一遍”通常收益有限。更强的路线是在模型自己的错误分布上训练，让它学会识别和修正自身常见错误。

这说明一个关键点：

> 自迭代不能只靠 prompt，反馈分布、训练目标、评估信号和错误归因同样重要。

## 4. 标准自迭代闭环

一个实用 Agent 自迭代系统通常包含 7 个模块：

```text
任务输入
  -> 计划生成
  -> 工具/环境执行
  -> 结果观测
  -> 评估打分
  -> 反思/归因
  -> 更新记忆、技能、策略或代码
  -> 下一轮执行
```

关键不是“会反思”，而是每一轮反馈都能落到可验证资产：

- 新增一条 memory。
- 修正一个 prompt。
- 生成一个 reusable skill。
- 改进一个 tool adapter。
- 更新一个 workflow graph。
- 增加一个 regression test。
- 改进 evaluator。
- 修改 agent scaffold code。

## 5. 当前成熟度判断

截至 2026 年，Agent 自迭代处于工程可用早期和研究快速分化期。

已经比较可行：

- 基于失败反馈的 retry / reflection。
- 代码任务中的 test-driven 自修复。
- 有明确 evaluator 的算法优化。
- 技能库积累。
- workflow / agent graph 的半自动优化。
- 多 Agent 互评和 judge Agent。

仍不成熟：

- 完全开放环境中的长期自我改进。
- 无客观评估函数的自我提升。
- 长期记忆不污染。
- 自生成 reward 的可信性。
- 自修改代码的安全边界。
- 跨任务泛化的持续收益证明。
- 真正意义上的递归自我改进。

## 6. 关键瓶颈

### 6.1 评估信号不足

没有可靠 evaluator，自迭代无法判断“变好了还是变坏了”。评估器是自迭代系统的地基，而不是上线后再补的附属模块。

### 6.2 反馈归因困难

任务失败可能来自模型推理、工具错误、上下文缺失、权限问题或环境变化。错误归因不准，就会把改进施加到错误位置。

### 6.3 记忆污染

Agent 可能把偶然经验、错误结论、过期信息写入长期记忆。长期记忆需要写入门槛、冲突检测、过期策略和质量评估。

### 6.4 自我强化偏差

如果 planner、executor、judge 都来自同一模型家族，系统容易互相确认错误。重要场景需要外部 evaluator、人类抽检或异构模型交叉评估。

### 6.5 安全边界

自修改工具、自生成代码、自调用 shell、自更新策略都可能引入越权、数据泄露和 sandbox escape 风险。

### 6.6 成本压力

多轮搜索、反思、评估、回放会显著增加 token、时间和算力成本。自迭代必须有预算控制和收益评估。

## 7. 工程落地路径

### P0：可观测 Agent

先记录 trace、tool call、错误、成本、输入输出。没有观测，就没有自迭代。

### P1：失败反馈闭环

每次失败后生成结构化反思：

```json
{
  "failure_type": "tool_error | planning_error | missing_context | bad_assumption",
  "root_cause": "...",
  "next_policy": "...",
  "should_write_memory": true
}
```

### P2：测试驱动自修复

尤其适合 coding agent：

```text
修改代码 -> 运行测试 -> 收集错误 -> 修复 -> 回归测试 -> 记录经验
```

### P3：技能库

把成功轨迹沉淀成可复用技能，而不是只存聊天记录。

### P4：Evaluator 优先

为每类任务建立自动评分器：

- test。
- schema check。
- diff check。
- benchmark。
- human review gate。

### P5：工作流自优化

先让 Agent 优化外围系统，而不是直接修改底层模型：

- tool selection policy。
- prompt template。
- workflow graph。
- retry strategy。
- context retrieval policy。
- approval policy。

### P6：受限自修改

允许 Agent 修改自己的局部代码，但必须经过：

- sandbox。
- diff review。
- tests。
- rollback。
- human approval。
- audit log。

## 8. 对 AiAgent 的定位建议

AiAgent 可以把自迭代定位为下一阶段主线：

> 在可观测、可评估、可回滚、可治理的边界内，把 Agent 的执行反馈转化为长期能力资产。

短期建议优先建设：

1. Trace 和任务回放。
2. 结构化失败归因。
3. Evaluator / golden tasks。
4. Memory 写入策略。
5. Skill library。
6. 受限自修改和人工审批。

