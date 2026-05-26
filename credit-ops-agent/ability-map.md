# LLM + Agent 工程能力地图

> 持续更新。空心节点（无内容）= 还没学到，不填。
> 来源标注格式：[项目名·关卡] 或 [资讯·日期]

---

## RAG 体系

### 检索策略
- **稠密检索（向量）**：Qdrant，embed 后 cosine 相似度，适合语义模糊匹配（"销售额" ≈ "营收"）。[data-agent·L4/L5]
- **稀疏检索（BM25）**：ElasticSearch，精确关键词匹配，适合离散枚举值（"M1"/"北京"）。[data-agent·L4/L5]
- **混合检索**：同一问题走两路，结果融合。data-agent 中 column/metric 走 Qdrant，value 走 ES。[data-agent·L5]
- HyDE（假设文档嵌入）：⬜ 待学（U2 knowledge_base）
- 多路召回与融合 RRF：⬜ 待学（U2 knowledge_base）

### 分块策略
- 2 段切片策略：⬜ 待学（U2 knowledge_base）

### 重排序
- Cross-Encoder Reranker：⬜ 待学（U2 knowledge_base）
- 断崖检测：⬜ 待学

### Query Expansion（查询扩展）
- **原理**：解决 Vocabulary Mismatch——用户词汇 ≠ 数据库词汇。[data-agent·L4]
- **实现**：jieba POS 过滤（12 种词性白名单）+ LLM 扩写 → 多路扩展词喂向量检索。[data-agent·L3/L4]
- **逐词独立 embed**：保持语义单元粒度，避免多词拼串后语义平均化漂移。[data-agent·L4]

### 生产级坑点
- 召回阶段"宽进"（threshold=0.6/limit=5）+ filter 阶段"严选"（LLM 二次过滤），两阶段缺一不可。[data-agent·L6/L7]

---

## Agent 体系

### 单 Agent 架构（节点 / 边 / 状态）
- **StateGraph 构建**：节点（node）= 函数，边（edge）= 数据流，条件边（conditional_edge）= 运行时路由。[data-agent·L2]
- **Fan-out / Fan-in**：同一起点 add_edge 到多节点 = 并发；多节点 add_edge 到同一汇聚点 = 自动等齐（barrier）。[data-agent·L2]
- **State vs Context 分离**：State 存业务数据（可序列化，跨节点传递）；Context 存外部客户端/仓储（不可序列化，只读依赖）。[data-agent·L1]
- **Partial State Update**：节点 return dict 只覆盖命中字段，其余字段原样保留。[data-agent·L3]
- **双通道输出**：return 写 State 给下游节点；writer 实时推送给前端（stream_mode="custom"）。[data-agent·L10]

### Multi-Agent 协作
- deep_search_pro 多 Agent 编排：⬜ 待学（U3）

### 状态管理
- **ContextVar 请求隔离**：一个变量名、每请求一份独立副本（asyncio 给每个任务发独立 Context 快照表）。[data-agent·L0]
- thread_id 会话隔离：⬜ 待学

### 工具调用与 MCP
- ⬜ 待学

---

## Text-to-SQL 体系

### Schema 理解
- **元数据双库**：meta_config.yaml 存结构化元数据 → 同步到 ES（全文）+ Qdrant（向量），和业务库（MySQL）完全分离。[data-agent·L0/L5]
- **Schema Enrichment**：merge 节点补主外键，给 LLM 提供 JOIN 锚点，防笛卡尔积/幻觉字段。[data-agent·L6]
- **迁移哲学**：换业务 ≈ 换 meta_config.yaml + 换 jieba userdict.txt，代码复用度 90%+。[data-agent·L12]

### SQL 生成与校验
- **YAML 喂 LLM**：比 JSON 省 20-30% token，allow_unicode=True 防中文转义，sort_keys=False 保顺序（LLM 顺序敏感）。[data-agent·L8]
- **时间锚定**（Temporal Grounding）：动态注入当前日期 + DB 方言，LLM 没有"现在"概念。[data-agent·L8]
- **EXPLAIN 前置校验**：无副作用、快，只校语法+表字段存在性，失败走修复路径。[data-agent·L9]

### 错误修正循环
- **有界重试三军规**：硬上限（次数）+ 单向链路防循环（correct 不回走 generate）+ 兜底兜住。[data-agent·L9/L10]
- **correct Prompt 比 generate 多 3 条约束**：基于 error 修正 / 保持原业务语义 / 最小必要修改——防"越改越错"。[data-agent·L10]

---

## 大模型底层

- Transformer 原理：⬜ 待学（面试 1.7 节）
- 训练（SFT / LoRA / RLHF）：⬜ 待学
- 推理优化：⬜ 待学

---

## 工程判断力

### 场景选型
- 离散枚举值 + 精确匹配 → ES BM25；自由文本 + 概念相关 → 向量库。[data-agent·L5]
- jieba 快/免费/可解释 vs LLM 贵/慢/易漂 → 第一节点用规则，不用 LLM。[data-agent·L3]

### 生产级取舍
- **execute_sql 零护栏**（高优）：无 LIMIT 兜底/无流式/无超时 → 四层护栏：Prompt 软约束 + SQL 硬兜底 + 流式拉数据 + 超时强杀。[data-agent·L10]
- **lifespan 启动零容错**（中优）：init 无 try → 资源泄漏 + 启动崩。应按依赖重要性选：重试/降级/Fail Fast。[data-agent·L11]
- **init/close 对称契约**（低优）：embedding_client 漏 close → SIGTERM 挂起 + TCP 泄漏。[data-agent·L11]
- **SSE 协议硬规范**：`data:` + `\n\n`，漏双换行 → 浏览器永远不触发 onmessage，沉默失败。[data-agent·L11]

### ⬜ 知识孤岛警告（待跨项目印证）
- 多路召回架构：目前只有 data-agent 一个项目经验，U2 knowledge_base 重点关注
- 向量库选型（Qdrant vs Milvus）：待 U2 补充对比
