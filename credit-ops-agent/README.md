# Credit Ops Agent · 项目总览

> **AI 接手协议**：新会话只需读本文件 + `progress.md` 最新一条，即可续接工作。

---

## 项目定位

- **目标**：转行「AI Agent 开发工程师」的简历级作品
- **业务**：信贷贷后智能运营助手——支持问数 / 文档问答 / 复杂催收决策
- **路径**：读懂 3 个现有项目 → 跑通 3 个项目 → 合并成 credit-ops-agent

---

## 当前状态

| 线 | 阶段 | 进度 |
|---|---|---|
| **理解线** | U1 data-agent 精读 | 🔄 进行中（L0 ✅ 通关，L1-L12 闯关中）|
| **实操线** | R1 跑通 data-agent | ⏳ 待开始 |

**理解线下一步**：把 [learning-journey.md](./learning-journey.md) @ 给 AI，说"继续学习"，当前在 L1《State vs Context 分离》。
**实操线下一步**：配置 data-agent `.env`，Docker 启动 MySQL + Qdrant + ES，本地验收 5 个 case。

---

## 双轨说明

**理解线（费曼学习法）**：每个项目对应一张旅程卡，闯关式问答，每关 Preview → Question → Explain → Feedback。

**实操线**：跑通每个项目（本地启动 + 验收 case），然后迭代实现 credit-ops-agent（M1-M4）。

两条线独立推进，不强制同步。理想节奏：同一周内理解线和实操线做同一个项目，互相印证。

---

## 文档地图

### 计划与进度
| 文档 | 作用 |
|---|---|
| [plan.md](./plan.md) | 双轨完整计划（U1-U7 理解线 + R1-R4 实操线，含验收标准）|
| [progress.md](./progress.md) | 进度日志（倒序，最新在顶）|

### 理解线旅程卡（闯关学习，切换会话时 @ 对应文件）
| 文档 | 对应阶段 | 说明 |
|---|---|---|
| [learning-journey.md](./learning-journey.md) | U1 · data-agent | 13 关，当前在 L1（L0 ✅）|
| [kb-learning-journey.md](./kb-learning-journey.md) | U2 · knowledge_base RAG | 9 关，待开始 |
| [deep-search-learning-journey.md](./deep-search-learning-journey.md) | U3 · deep_search_pro | 10 关，待开始 |

### 已产出的知识地图
| 文档 | 作用 |
|---|---|
| [../learning/data-agent-architecture.md](../learning/data-agent-architecture.md) | U1 交付物：架构图 + 时序图 + 三大面试考点 |
| [../learning/notes-langchain-langgraph.md](../learning/notes-langchain-langgraph.md) | LangChain/LangGraph 基础笔记（U1 前置知识）|
| [../specs/data-agent-spec.md](../specs/data-agent-spec.md) | data-agent 规格说明书（U4 能力抽象的输入）|

### 待产出（按阶段逐步创建）
- `docs/learning/knowledge-base-architecture.md`（U2 交付物）
- `docs/learning/deep-search-pro-architecture.md`（U3 交付物）
- `capability-matrix.md`（U4）
- `prd.md`（U5）
- `spec.md`（U6）

---

## 工作方式

**开工前**：读本文件 + `progress.md` 最新一条。

**理解线学习时**：把对应旅程卡 @ 给 AI，说"继续学习 [项目名]"，不需要读其他文档。

**实操线 / 编码时**：读本文件 + `progress.md` 最新一条，然后说明当前任务。

**每次收工**：更新 `progress.md`（做了什么 / 卡在哪 / 下一步）+ 更新旅程卡 §4 进度快照。

**阶段收官**：更新本文件「当前状态」一节 + 花 30min 写简历条目。

---

## 技术栈预设

| 层 | 技术 |
|---|---|
| 编排 | LangGraph（确定性链路）+ deepagents（多 Agent 路由）|
| LLM | 复用 data-agent 的模型配置 |
| RAG | RAGFlow（托管 RAG 服务）|
| 结构化存储 | MySQL + Qdrant + ES（复用 data-agent 方案）|
| API | FastAPI + SSE |

**源码位置**（R4 阶段创建）：`/Users/ys/code/ai_agent/credit-ops-agent/`
