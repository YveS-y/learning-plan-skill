# Credit Ops Agent · 双轨计划

> **理解线**（费曼学习法，读懂每个项目）+ **实操线**（跑通每个项目）→ 合并成 credit-ops-agent
>
> 每日投入 2-3h，预计 6-8 周。两条线各有独立进度，互相独立推进，不强制同步。

---

## 理解线（U · Understanding）

**目标**：用费曼学习法读懂三个项目的核心设计，最终产出 credit-ops 的 PRD + Spec。
**学习方式**：闯关式问答（见各项目的 `*-learning-journey.md`），每关 Preview → Question → Explain → Feedback。

---

### U1 · data-agent 精读 ✅ 完成（2026-05-08）

- **交付物**：`docs/learning/data-agent-architecture.md`（架构图 + 时序图 + 三大面试考点）
- **学习卡**：[learning-journey.md](./learning-journey.md)（L0-L12 + Final 全通关）
- 三大考点：多路异构召回 / SQL 生成 Prompt 设计 / SQL 自愈机制
- 生产护栏 Backlog 3 条（execute_sql 零护栏 / lifespan 零容错 / embedding 漏关闭）
- **待补**：简历项目描述初版（U7 规则：收官后 30min，已欠账，本周补）

---

### U2 · knowledge_base RAG 链路精读 🔄 进行中（2026-05-11 开启）

> knowledge_base 有 59 个 .py 源文件，完整实现了自建 RAG：导入图（7 节点）+ 查询图（7 节点），技术栈 Milvus + MongoDB + MinIO + BGE-M3 + Reranker + MinerU。

- [ ] 读 `knowledge_base/app/import_process/agent/main_graph.py`（7 节点导入图，条件路由）
- [ ] 读 `knowledge_base/app/import_process/agent/nodes/`（PDF 解析 → 2 段切片 → BGE-M3 向量化 → Milvus 入库）
- [ ] 读 `knowledge_base/app/query_process/agent/main_graph.py`（7 节点查询图，3 路并行召回）
- [ ] 读 `knowledge_base/app/query_process/agent/nodes/`（HyDE、RRF、Cross-Encoder Reranker）
- [ ] 总结三大面试考点：2 段切片策略 / HyDE + RRF 多路召回 / Reranker 精排
- [ ] **交付物**：`docs/learning/knowledge-base-architecture.md`
- **学习卡**：[kb-learning-journey.md](./kb-learning-journey.md)（10 关）
- **简历穿插**：收官后 30min 把 RAG 三大考点补进简历

---

### U3 · deep_search_pro 多 Agent 编排精读（4-5 天）

- [ ] 读 `agent/main_agent.py`：`create_deep_agent` 与 LangGraph 的区别
- [ ] 读 `agent/subagents/`：三个子智能体的定义方式（dict vs compiled graph）
- [ ] 读 `tools/`：6 个工具的设计模式（@tool 装饰器、埋点、异常处理）
- [ ] 读 `api/server.py` + `api/monitor.py`：WebSocket vs SSE 实时通信
- [ ] 读 `api/context.py`：ContextVar 会话隔离（对比 data-agent）
- [ ] 总结三大面试考点：多 Agent 编排 / 工具调用链 / WebSocket 监控
- [ ] **交付物**：`docs/learning/deep-search-pro-architecture.md`
- **学习卡**：[deep-search-learning-journey.md](./deep-search-learning-journey.md)（10 关）
- **简历穿插**：收官后 30min 补进简历

---

### U4 · 能力抽象（2 天）

- [ ] 三项目能力矩阵：结构化查询 / RAG / 多 Agent 编排 / Prompt 管理 / 评测
- [ ] 哪些模块可直接复用 / 需改造 / 需重写（参考各旅程卡的"迁移设计"关卡）
- [ ] **交付物**：`docs/credit-ops-agent/capability-matrix.md`

---

### U5 · PRD 产品需求（3-4 天）

- [ ] 5-7 个用户故事（覆盖问数 / 文档问答 / 复杂决策三类场景）
- [ ] **Mock 数据策略拍板**（此阶段必须决定）：
  - 方案 A（推荐）：自写生成脚本，4-5 张信贷表，50 万条
  - 方案 B（兜底）：复用 data-agent demo 数据集改名适配
- [ ] Mock 数据 schema：用户主表、放款表、还款表、贷后标签表
- [ ] PDF 文档清单：催收话术、合规政策、产品说明书、运营 SOP
- [ ] **交付物**：`docs/credit-ops-agent/prd.md`

---

### U6 · Spec 技术规格（4-5 天）

- [ ] 系统架构图：Router Agent + SQL Sub Agent + RAG Sub Agent + 工具层
- [ ] 数据模型：MySQL DDL + 向量库 schema
- [ ] API 契约：REST + SSE
- [ ] 关键 Prompt 草稿（每个 Agent 一份）
- [ ] 评测集：30-50 题，含三类场景，标注答案
  - M1 验收题（10 题 RAG 问答）
  - M2 验收题（10 题 SQL 问数）
  - M3 验收题（10-20 题混合路由）
- [ ] **交付物**：`docs/credit-ops-agent/spec.md`

---

### U7 · 简历与面试（穿插全程）

> 规则：每个 U 阶段收官后立即花 30min 写，不攒到最后。

- [ ] U1 后：三大考点 → 简历项目描述初版
- [ ] U2 后：RAG 考点 → 补充简历
- [ ] U3 后：多 Agent 编排 → 补充简历
- [ ] U6 后：STAR 话术 3 套（架构决策 / Prompt 调优 / Bug 排查）
- [ ] 收尾：README 英文版 + 架构图 + 一键启动 + 3 分钟 Demo 视频

---

## 实操线（R · Run）

**目标**：让三个现有项目在本地跑起来，最终从零迭代出 credit-ops-agent。

---

### R1 · 跑通 data-agent（2 天）

- [ ] 配置 `.env`：LLM key、MySQL、Qdrant、ElasticSearch
- [ ] Docker 启动 MySQL + Qdrant + ES
- [ ] 运行元知识构建脚本（同步元数据到向量库 / ES）
- [ ] 启动 `uvicorn main:app`，健康检查通过
- [ ] 手写 5 个 case 调用 API（覆盖：单表 / 多表 JOIN / 含指标 / 含时间范围 / 含渠道筛选）
- **验收标准**：5 个 case 全部返回正确 SQL + 查询结果，无错误退出

---

### R2 · 跑通 knowledge_base（2-3 天）

> knowledge_base 是完整自建 RAG，独立于 RAGFlow。技术栈：Milvus + MongoDB + MinIO + BGE-M3 本地模型。

- [ ] 配置 `.env`：LLM key、Milvus、MongoDB、MinIO 连接信息
- [ ] Docker 启动 Milvus + MongoDB + MinIO（参考 `knowledge_base/CLAUDE.md`）
- [ ] 下载 BGE-M3 + Reranker 模型（本地推理）
- [ ] 运行导入流程：上传 3+ 份测试 PDF，验证 Milvus 已入库
- [ ] 运行查询流程：问 3 个问题，验证三路并行召回 + RRF + Rerank 链路
- **验收标准**：3 次问答均返回含正确来源段落的回答，RRF + Rerank 链路无报错

---

### R3 · 跑通 deep_search_pro（2-3 天）

- [ ] 配置 `.env`：LLM key、RAGFlow API key + base_url、Tavily API key
- [ ] 启动 `api/server.py`（uvicorn），WebSocket 端点正常
- [ ] 用 Postman / curl 发 `POST /api/task`，WebSocket 收到实时消息
- [ ] 三类场景各问一题：知识库问答 / DB 查询 / 联网搜索
- [ ] 验证 `monitor` 推送链路：tool_start / assistant_call / task_result 均能收到
- **验收标准**：三类场景均返回有效结果，WebSocket 消息链路完整可观测

---

### R4 · credit-ops-agent 迭代（U6 完成后启动，约 4 周）

#### M1 · RAG 问答基础链路（4 天）
- [ ] 脚手架：目录结构、配置加载、日志
- [ ] 接入 RAGFlow：PDF 入库（催收/合规文档 3+篇）
- [ ] FastAPI + SSE 接口
- [ ] 跑通：`POST /query` → RAGFlow 检索 → LLM 回答 → SSE 流式返回
- **验收标准**：M1 验收题 10 题，≥ 7 题回答含正确来源段落

#### M2 · SQL 问数链路（5 天）
- [ ] Mock 数据入库（按 U5 拍板的方案）
- [ ] 元数据配置（`meta_config.yaml`：信贷表、字段、指标）
- [ ] 移植 data-agent SQL 生成 Agent，适配信贷业务 Prompt
- [ ] 跑通：问题 → SQL → 查询结果
- **验收标准**：M2 验收题 10 题，≥ 8 题 SQL 语法正确且结果符合预期

#### M3 · Router + 多 Agent 编排（5 天）
- [ ] Router Agent：判断问题类型 → 分发到 RAG / SQL / 复杂决策子 Agent
- [ ] 复杂决策：多步推理 + 工具链（参考 deep_search_pro subagent 模式）
- [ ] 会话上下文管理（thread_id 隔离）
- **验收标准**：M3 验收题 10 题混合路由，准确率 ≥ 80%

#### M4 · 评测 + 调优（4 天）
- [ ] 评测脚本：跑 30-50 题评测集，输出三类场景准确率报告
- [ ] 监控链路（参考 deep_search_pro monitor 模式）
- [ ] Prompt 调优直到各类场景 ≥ 75%
- **验收标准**：评测报告产出，整体 ≥ 75%，Demo 可录屏

---

## 双轨进度对照表（重置于 2026-05-11）

> 求职截止 2026-05-25，剩余 14 天。U1 已于 2026-05-08 全毕业。  
> credit-ops-agent 完整实现（R4 M1-M4）延后到找到工作后继续。

| 日期 | 理解线（U）| 求职输出 |
|------|-----------|---------|
| 5/11-5/14 | **U2** knowledge_base（kb-learning-journey.md，目标 L0-L6）| RAG 理论题整理 |
| 5/15-5/17 | **U3** deep_search_pro 速读（架构层，不求全通关）| 多 Agent 考点整理 |
| 5/18-5/19 | **U7 补账**：data-agent 三大考点 → 简历项目描述初版 | 简历初稿 |
| 5/20-5/22 | **U7**：RAG + 多 Agent 考点补进简历 + 项目串讲稿 | 能流畅讲 5 分钟 |
| 5/23-5/25 | — | 面试突击 + 模拟（出声，计时）|
| 求职后 | U4-U7 继续 + R1-R4 完整实操 | credit-ops-agent 落地 |

---

## 风险与降级

| 风险 | 降级 |
|---|---|
| RAGFlow Docker 部署卡住 | 用 Qdrant + LangChain 自建 RAG 替代 R2 |
| Mock 数据工作量大 | 执行方案 B：data-agent demo 数据集改名适配 |
| M3 多 Agent 编排过难 | 降级为关键词规则路由 |
| 时间超支 | 砍掉 M4 监控，保 M1-M3 跑通 + 评测脚本 |
