# AiAgent

> 更新日期：2026-06-18

AiAgent 是一个面向智能体系统的研究与工程路线图仓库，目标是把“AI Agent 当前发展态势”和“核心原子能力的迭代路径”沉淀成可持续维护的知识底座。

## 当前发展摘要

AI Agent 正在从“单轮问答 + 工具调用”进入“可运行、可观测、可协作、可治理”的工程阶段。行业共识已经比较清晰：大模型本身负责理解、推理和生成，Agent Runtime 负责循环执行、工具调度、状态管理、权限控制、人类确认和结果追踪。

几个关键变化：

1. **工具调用标准化**：MCP 等协议把模型、工具、数据源、工作流之间的连接抽象出来，减少每个工具都单独适配的集成成本。
2. **运行时工程化**：Agent SDK / LangGraph / OpenHands 等框架把 agent loop、handoff、sandbox、session、tracing、human-in-the-loop 做成稳定组件。
3. **从 Demo 到任务闭环**：Agent 的价值不再是“能不能调用工具”，而是能否在真实文件、浏览器、数据库、代码仓库和业务系统中完成多步骤任务，并留下可审计轨迹。
4. **安全成为核心能力**：工具投毒、提示注入、越权访问、供应链风险、敏感数据泄露成为 Agent 系统上线前必须处理的问题。
5. **多 Agent 协作更务实**：从“人格化角色扮演”转向任务分解、专家工具化、handoff、并行执行、冲突检测和统一评估。

## 核心原子能力

Agent 的能力可以拆成 10 个原子模块，每个模块都能单独评估和迭代：

| 能力 | 含义 | 关键产物 |
| --- | --- | --- |
| 感知输入 | 理解用户意图、环境状态、文件/网页/数据库上下文 | context packet、任务画像 |
| 任务规划 | 拆解目标、排序步骤、识别依赖和风险 | plan、DAG、检查点 |
| 工具调用 | 选择工具、构造参数、解析结果、重试失败 | tool registry、schema、adapter |
| 记忆管理 | 短期上下文、长期偏好、项目知识、经验复用 | session、memory store、retrieval policy |
| 执行控制 | agent loop、状态机、暂停/恢复、超时和幂等 | runtime、checkpoint、resume token |
| 多 Agent 协作 | 委派、handoff、专家代理、并行任务合并 | role graph、handoff contract |
| 人机协同 | 关键节点确认、审批、纠错、可编辑中间产物 | approval gate、review UI |
| 评估反馈 | 单元测试、任务成功率、轨迹评分、回归集 | eval suite、golden tasks |
| 安全治理 | 权限、隔离、审计、敏感信息保护、工具风险控制 | policy engine、sandbox、audit log |
| 观测运维 | tracing、日志、成本、延迟、失败模式分析 | trace/span、metrics、dashboard |

## 迭代路径

推荐按“最小闭环 -> 可靠执行 -> 协作扩展 -> 生产治理”推进：

1. **P0：单 Agent 最小闭环**
   - 支持自然语言任务输入。
   - 支持 3-5 个稳定工具。
   - 完成一次 plan / act / observe / final loop。
   - 建立基础日志和任务结果记录。

2. **P1：可恢复的工程运行时**
   - 引入 session、checkpoint、失败重试、超时控制。
   - 对工具 schema 做严格校验。
   - 把高风险工具放入审批或沙箱。
   - 建立任务回放和 trace 查看能力。

3. **P2：上下文和记忆系统**
   - 区分用户偏好、项目知识、临时上下文。
   - 支持检索增强和上下文压缩。
   - 引入记忆写入策略，避免污染长期记忆。
   - 建立“记忆是否真的提升任务成功率”的评估。

4. **P3：多 Agent 和复杂工作流**
   - 支持专家 agent 作为工具被调用。
   - 支持 handoff、并行子任务、结果合并。
   - 为每类专家定义输入输出契约。
   - 对多 agent 协作引入冲突检测和仲裁。

5. **P4：生产级治理**
   - 权限最小化、密钥隔离、审计日志。
   - Prompt injection 和 tool poisoning 防护。
   - 灰度发布、成本预算、质量回归评估。
   - 建立人工接管、任务撤销和事故复盘机制。

## 建议仓库结构

```text
AiAgent/
├── README.md
├── docs/
│   ├── atomic-capability-roadmap.md
│   └── references.md
└── .gitignore
```

## 参考资料

- [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/)
- [Model Context Protocol](https://modelcontextprotocol.io/docs/getting-started/intro)
- [LangGraph overview](https://docs.langchain.com/oss/python/langgraph/overview)

## 专题文档

- [核心原子能力迭代路径](docs/atomic-capability-roadmap.md)
- [Agent 自迭代领域综述](docs/self-iteration-overview.md)
- [参考资料索引](docs/references.md)
