# Slack Engineering: Managing Context in Long-run Agentic Applications

## Objective
分析 Slack Engineering 博客文章：多智能体长程推理系统中的上下文管理机制。

## Key Findings

### 核心问题
- LLM API 无状态，需调用方自行维护完整消息历史
- Agent 框架通过累积消息历史来解决状态管理，但会撑满 context window
- 复杂安全调查可能跨越数百次推理请求，产生数 MB 输出
- 多智能体场景：每个 agent 需要量身定制的调查状态视图
- **关键矛盾**：共享过多 → 扼杀创造力+确认偏误；共享不足 → 调查脱节

### 三通道上下文架构

#### 1. Director's Journal（导演日志）
结构化工作记忆，6种条目类型：

| 类型 | 用途 | 示例 |
|------|------|------|
| decision | 战略选择 | "聚焦认证异常而非网络活动" |
| observation | 发现的模式 | "多次失败登录后成功认证" |
| finding | 已确认事实 | "用户从 IP 203.0.113.45 认证" |
| question | 开放问题 | "VPN 连接在可疑活动前还是后建立？" |
| action | 已做/计划步骤 | "请求云专家检查 EC2 实例活动" |
| hypothesis | 工作中理论 | "凭证填充而非账户被盗" |

每个条目含：优先级、后续行动、引证到证据工件、相位+轮次+时间戳。
所有 agent 都收到日志全文（作为时间序呈现），系统提示词解释导演角色和如何使用日志。
→ 功能：让导演领导调查方向、观察进度、识别死胡同、做出修正，为所有 agent 提供共同叙事锚点。

#### 2. Critic's Review（评论员审查）
- 作用：检查 Experts 的发现是否有证据支持，解读是否合理
- 工具集（4个工具）：`get_tool_call`（检查参数）、`get_tool_result`（检查输出）、`get_toolset_info`（工具文档）、`list_toolsets`（可用工具集列表）
- 输出：带评分的注释发现报告 + 整体摘要

#### 3. Critic's Timeline（时间线）
- 输入：最新 Review + 上一个 Timeline + Director's Journal
- 约束：仅含可信引证支持的事件；去除重复；时间戳冲突时选证据更强来源；保持时间序
- 识别三类空白：证据空白（缺失数据）、时间空白（无法解释的期间）、逻辑不一致
- 限制：仅报告最关键的 3 个空白，避免信息过载

### 评分体系（五级可信度）

| 分值 | 标签 | 标准 |
|------|------|------|
| 0.9-1.0 | Trustworthy | 多源支持，无矛盾指标 |
| 0.7-0.89 | Highly-plausible | 单源佐证 |
| 0.5-0.69 | Plausible | 混合证据 |
| 0.3-0.49 | Speculative | 证据支持薄弱 |
| 0.0-0.29 | Misguided | 无证据或误读 |

170,000 条审查发现分布：Trustworthy 37.7%，Highly-plausible 25.4%，Plausible 11.1%，Speculative 10.4%，Misguided 15.4%。

### 对抗幻觉的三重防线

1. **更强模型**：Critic 使用更强模型（因为只看已提交发现，token 量可控）；更强的模型推理能力更好，幻觉更少
2. **窄化指令**：将问题范围严格限定在"仅对提交发现做判断"，避免开放性大题导致的幻觉
3. **时间线叙事强制**：必须与完整证据链一致才能保留；矛盾或缺乏叙事支持的发现被剪枝

### 消息历史策略（关键结论）
> "Besides these channels, we do not pass any message history forward between agent invocations. Collectively, these channels provide a means of online context summarisation, negating the need for extensive message histories."

**激进设计：完全不在 agent 调用之间传递消息历史**。即使 context window 无限大，累积的消息历史也会妨碍 agent 适当响应新信息的能力。

### 三角色架构

| 角色 | 职责 |
|------|------|
| **Director** | 编排调查，决定问什么问题、召唤哪些专家、几时结束 |
| **Experts** | 各有领域专长 + 工具，收集证据 |
| **Critic** | 审查专家工作，评估证据可信度，构建时间线 |

## Conclusions

1. **上下文管理是关键工程问题**，不是模型能力问题——精心设计的上下文通道可以克服 context window 限制
2. **三层分离架构**（日志/审查/时间线）比单一消息历史更有效：每层服务不同目的，提供 agent 所需信息而不压垮任何一方
3. **无需消息历史** 是一个反直觉但有价值的结论——结构化总结优于原始累积
4. **时间线叙事约束**是防止幻觉的第三道防线：幻觉只有在比真实观察更具叙事连贯性时才能存活
5. **五级可信度** + 数据分布（170k 发现）提供了真实的运营指标参考

## 对我的借鉴
- 我当前的 AGENTS.md 中已有"热/温/冷数据分层"设计，但可以进一步细化为：热层=当前任务状态、温层=Journal式结构化记忆、冷层=档案总结
- Critic 角色的"证据核查"设计值得借鉴：任何 AI 生成内容在进入长期记忆前都应经过类似的可信度评估
- "不传递消息历史"原则：在任务切换时，应该输出结构化总结而非保留完整对话历史