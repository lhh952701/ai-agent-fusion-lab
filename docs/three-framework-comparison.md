# Hermes vs Slack vs OpenClaw 架构对比分析

## 分析日期
2026-04-21

## 一、概览对比

| 维度 | Hermes Agent (NousResearch) | Slack Multi-Agent | OpenClaw |
|------|------|------|------|
| **定位** | 单体自进化 Agent | 多角色安全调查系统 | 多智能体编排平台 |
| **Stars** | ~22,000+ | N/A（企业内部） | N/A（开源产品） |
| **核心哲学** | 越用越强（Built-in Learning Loop） | 调查协同 + 防幻觉 | 安全编排 + Skills 生态 |
| **架构类型** | 单体 Agent + 多平台接入 | 多角色协同（Director/Expert/Critic） | 多层编排 + Subagent 派发 |
| **自进化** | ✅ 内置四层记忆 + 自写 Skill | ❌ 无（专注当前调查质量） | ❌ 无（依赖外部 Skills） |
| **防幻觉机制** | Critique（窄化指令 + 渐进式摘要） | Critic 评分 + Timeline 叙事强制 | 安全扫描（外部护盾） |
| **记忆管理** | 4层（Prompt/SessionArchive/Skill/External） | 3通道（Journal/Review/Timeline） | Workspace 文件 + LCM 压缩 |
| **执行模式** | Plan→Execute→Replan 动态循环 | 调查相位循环（Director 主导） | WO_QUEUE 入口 → Ops 执行 → Audit 验收 |

---

## 二、架构分层对比

### 2.1 Hermes Agent — 四层解耦

```
接入层（Interface）
├── CLI / TUI
├── 15+ 消息平台（Terminal / Telegram / Discord / Slack / WeChat / DingTalk / WhatsApp ...）
└── API Server

Agent 执行层（Core Loop）
├── run_agent.py（主对话循环）
├── model_tools.py（工具调度）
├── 上下文压缩
└── 错误分类重试

能力层（Capabilities）
├── 70+ 内置工具
├── 技能系统（自动生成 SKILL.md）
├── 内存系统（四层）
└── 沙箱环境

模型层（Model Abstraction）
├── OpenAI / Anthropic / OpenRouter / DeepSeek / Qwen / Groq / xAI ...
└── Nous Portal（零配置）
```

### 2.2 Slack Multi-Agent — 三角色协同

```
用户输入
    ↓
[Director] 导演
├── 编排调查方向（决定问什么、召谁、何时结束）
├── Journal（6种条目类型：decision/observation/finding/question/action/hypothesis）
└── 维护共同叙事锚点
    ↓ 分发给 Experts
[Expert × N] 各领域专家
├── 工具调用收集证据
└── 输出 findings（含证据工件引证）
    ↓
[Critic] 评论员
├── Review：检查证据链 + 4工具（get_tool_call/result/set_info/list_toolsets）
├── 五级可信度评分（0.9 Trustworthy → 0.1 Misguided）
└── Timeline：叙事一致性强制 + 三类空白识别
    ↓
[Director] 下一轮循环
```

### 2.3 OpenClaw — 五层编排

```
L0 Guardrails — 单一可信链路（Main → Ops → Audit）
L1 Orchestration — WO_QUEUE 串行锁（max_concurrent=1）
L2 Domain Router — life / dev / hybrid / system
L3 Skill Plane — 技能注册表
L4 Memory Plane — 任务恢复 + 知识记忆
```

---

## 三、记忆系统深度对比

### 3.1 Hermes — 四层记忆（最具野心）

| 层级 | 作用 | 存储方式 | 类比 |
|------|------|----------|------|
| L1 提示词记忆 | 自我认知 + 用户画像 | MEMORY.md + USER.md（~3575字符快照） | 冰山水上部分 |
| L2 会话归档 | 历史对话复用 | SQLite FTS5 + LLM 摘要 | 冰山水下表层 |
| L3 技能记忆 | 程序性知识 | SKILL.md 文档（agentskills.io 标准） | 肌肉记忆 |
| L4 外部接口 | 深度用户建模 | Honcho / Mem0 等外部服务 | 扩展大脑 |

**渐进式披露**：默认只加载 Skill 摘要，完整版按需加载，控制 Token。

### 3.2 Slack — 三通道上下文

| 通道 | 功能 | 关键机制 |
|------|------|----------|
| Director's Journal | 导演结构化工作记忆 | 6种条目 + 优先级 + 引证 + 时间戳 |
| Critic's Review | 证据核查 + 可信度评分 | 4工具审视证据链，0-1评分 |
| Critic's Timeline | 叙事一致性强制 | 仅含可信发现，自动剪枝矛盾内容 |

**激进设计**：不在 agent 调用之间传递消息历史，三通道本身就是在线上下文摘要。

### 3.3 OpenClaw — Workspace 文件 + LCM

- Workspace 文件（MEMORY.md / AGENTS.md / USER.md / SOUL.md）作为持久记忆
- LCM（Lossless Context Management）压缩历史
- Skill 系统支持动态扩展
- 相比之下：记忆设计更"手动"，不如 Hermes 自动化

---

## 四、防幻觉机制对比

| 框架 | 机制 | 效果 |
|------|------|------|
| **Hermes** | ① 窄化指令（只评提交发现） ② 渐进式摘要减少上下文噪声 | 中等（依赖模型能力） |
| **Slack** | ① 叙事一致性强制（幻觉必须比真实观察更具叙事连贯性才能存活）② 五级可信度评分 ③ 更强 Critic 模型 | **最强**（叙事强制剪枝） |
| **OpenClaw** | ① SUNGLASSES MCP 安全扫描 ② Audit 层验收 ③ 无内置批判机制 | 较弱（依赖外部护盾） |

**关键洞察**：Slack 的 Timeline 叙事强制是最独特的防幻觉设计——不是检查单条发现是否正确，而是检查发现是否与完整证据链叙事一致。矛盾的内容自动被剪枝。

---

## 五、自进化机制对比

### Hermes 闭合学习回路

```
Execute（执行 + 记录步骤）
    ↓ [每15次工具调用]
Evaluate（后台复盘系统自动评估）
    ↓ [有价值经验]
Refine（生成/更新 SKILL.md 补丁）
    ↓
Evolve（下轮任务调用更新后的 Skill）
```

**关键**：Skill 补丁式迭代，不覆盖重写；完全异步，不阻塞用户。

### OpenClaw 自进化

- AGENTS.md 中已写入"四阶段自进化闭环"框架
- 但实际执行依赖外部 Skills 的学习和更新
- **差距**：没有 Hermes 那种自动从执行经验中提炼 Skill 的机制

### Slack — 无自进化

- 专注单次调查质量，不跨调查积累
- 设计哲学：质量 > 积累

---

## 六、执行循环对比

| 框架 | 循环类型 | 特点 |
|------|----------|------|
| **Hermes** | Plan→Execute→Replan | 动态重规划，应对步骤失败 |
| **Slack** | Phase→Round→Phase | Director 主导，固定调查阶段 + 多轮迭代 |
| **OpenClaw** | WO_QUEUE→Ops→Audit | 串行任务队列，Main 指挥官角色 |

---

## 七、工程化特色对比

| 维度 | Hermes | Slack | OpenClaw |
|------|--------|-------|----------|
| 部署方式 | 6种后端（Local/Docker/SSH/Modal...） | 企业内部服务 | 桌面应用 + Gateway |
| 多平台 | 15+ 消息平台网关 | 内部调查工具 | WebChat + 扩展 |
| 安全机制 | 5层防线 + 容器隔离 | 内网 + 审计日志 | SUNGLASSES + 安全规则 |
| 异步复盘 | ✅ Fork 守护进程 | ✅ 内置异步 | ❌ 同步 |
| 安装门槛 | 一行 curl（Linux/macOS） | 企业部署 | 桌面安装向导 |
| 中文支持 | 飞书/钉钉/企微原生 | 企业内部 | 企微/QQ 扩展 |

---

## 八、相互借鉴价值

### 从 Slack 借鉴到 Hermes/OpenClaw：
1. **Timeline 叙事强制** — 最强防幻觉机制，可移植
2. **Critic 五级可信度** — 可引入任何 AI 输出校验流程
3. **Journal 结构化记忆** — 替代纯消息历史

### 从 Hermes 借鉴到 Slack/OpenClaw：
1. **Skill 自动生成** — 当前调查经验 → 可复用技能文档
2. **异步后台复盘** — 不阻塞主对话的学习
3. **渐进式披露** — 大型记忆/技能库的 Token 控制
4. **补丁式迭代** — 增量更新而非覆盖重写

### 从 OpenClaw 借鉴到 Hermes/Slack：
1. **多 Subagent 派发** — 并行执行独立子任务
2. **WO_QUEUE 串行锁** — 任务编排的有序性保证
3. **Session 隔离** — 独立会话防止状态污染

---

## 九、综合评价

| 场景 | 推荐 | 理由 |
|------|------|------|
| **长期个人 AI 助手** | Hermes | 自进化 + 记忆积累 + 多平台 + 越用越强 |
| **企业安全调查** | Slack 架构 | 最强防幻觉 + 多角色协同 + 审计合规 |
| **多任务编排执行** | OpenClaw | Subagent 派发 + Skills 生态 + 安全编排 |
| **需要两者结合** | Hermes + Slack Critic | Hermes 执行力 + Slack 防幻觉 = 最优解 |

---

## 十、关键结论

**这三个系统代表了三种不同的 Agent 设计哲学：**

1. **Hermes = 深度学习型单体** — 核心是"让单一 Agent 越用越聪明"，记忆和 Skill 是核心资产
2. **Slack = 协同验证型多体** — 核心是"多角色互相制衡防错误"，批判性思维内化为架构
3. **OpenClaw = 安全编排型多体** — 核心是"安全可靠地执行任务"，编排和隔离是优先项

**最值得学习的架构设计：**
- **Timeline 叙事强制**（Slack）— 最反直觉但最有效的防幻觉设计
- **Skill 自动生成**（Hermes）— 将执行经验转化为可复用资产
- **异步后台复盘**（Hermes）— 学习不阻塞主流程

**对我自身的借鉴**（见 AGENTS.md）：
- Critic 角色的可信度评估 → AI 内容进入长期记忆前的质检流程
- 不传递消息历史 → 任务切换时输出结构化总结
- Timeline 叙事强制 → 记忆压缩时优先保留叙事一致性
