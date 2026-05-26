# Credit Ops Agent · 进度日志

> 倒序记录，最新的在最上面。每次开工前 AI 读顶部一条即可续接。

---

## [2026-05-16] L5 查询图核心理解完成，Spec 待写

**当前双轨状态**：

| 线 | 阶段 | 进度 |
|---|---|---|
| 理解线 | U2 knowledge_base | 🔄 L5 进行中，核心理解完成，Spec 待写 |

**今日完成**：
- ✅ 确认「查询向量 vs 过滤条件」区别：向量化的是 rewritten_query，item_names 拼成 Milvus expr 过滤范围
- ✅ ranker_weights=(0.9, 0.1) 判断力题：信贷合规场景条文编号/专业术语需精确匹配 → 应调高稀疏权重（如 0.5/0.5 或 0.3/0.7）
- ✅ 复盘导入图主线（L0-L4.5），纠偏 BGE-M3 / MongoDB 元数据 / 图片说明写回 Markdown
- ✅ 查询图骨架能口述：item_name 确认 → 三档路由 → 3路并行召回

**下一步**：
- 写 L5 节点 Spec（50-80 字，做什么/不做什么/关键取舍）→ 归档 §8
- 继续 step_5 → step_6（可选，已有足够素材写 Spec）

---

## [2026-05-11] 计划重置 · 14 天求职冲刺启动

**当前双轨状态**：

| 线 | 阶段 | 进度 |
|---|---|---|
| 理解线 | U2 knowledge_base | 🔄 今日开启，kb-learning-journey.md 已就绪 |
| 实操线 | R1 data-agent | ⏳ 延后到求职后 |

**今日完成**：
- 确认 U1 已于 5/8 全毕业（L0-L12 + Final 口述全通关）
- 重置 plan.md 双轨进度表：聚焦 5/11-5/25 的 14 天冲刺
- 整合 3 个 Project 学习工具说明（战场层/雷达层/框架层）进入 plan.md

**下一步**：
- 打开 [kb-learning-journey.md](./kb-learning-journey.md)，新会话说「继续学习 RAG」从 L0 开始
- 目标：5/14 前通关 U2 关键关卡（HyDE / RRF / Rerank）

---

## 📌【常驻 Backlog】data-agent → credit-ops-agent 必须补强的点

> 学习过程中发现的、data-agent 里可以/必须改进的点。credit-ops-agent 参考 data-agent 架构时，**这些点必须补强**。本区段始终置顶维护，通关一条勾一条。

### 🛡️ 生产护栏类

- [ ] **execute_sql 零护栏** ⚠️ 高优先级（发现于 L10 · 2026-04-30）
  - **位置**：`data-agent/app/repositories/mysql/dw/dw_mysql_repository.py` 第 30-32 行
  - **现状**：`session.execute(text(sql))` → `fetchall()` 一把梭，**无 LIMIT 兜底、无流式、无超时**
  - **风险场景**：
    1. LLM 写出 `SELECT * FROM fact_order`（十亿行）→ `fetchall` 一次性加载 → **Python 进程 OOM**
    2. LLM 写出 `SELECT COUNT(*) FROM 大表` → 只返回 1 行但**扫全表**，LIMIT 挡不住，拖死连接池
    3. 复杂 JOIN / 笛卡尔积 → 能跑但极慢，**占用连接不释放**
  - **四层护栏方案**（从便宜到硬核）：
    1. **Prompt 软约束**：`generate_sql.prompt` 加「必须 LIMIT 100，除非用户明说全部」
    2. **SQL 硬兜底**：执行前检查，无 LIMIT 自动拼 `LIMIT 1000`
    3. **流式取数**：`session.stream()` 替代 `execute`+`fetchall`，配合 `async for` + 手动 break
    4. **查询超时**：连接层设置 `statement_timeout` / MySQL `MAX_EXECUTION_TIME = 30s` 强杀
  - **记忆口诀**：**Prompt 软约束 + SQL 硬兜底 + 流式拉数据 + 超时强杀**——四道闸门一起上，才叫生产级
  - **反例警示**：只加 LIMIT 兜底挡不住 `COUNT(*)` 类查询，**超时层不可省**
  - **credit-ops-agent 必补理由**：信贷场景大表（流水表、账户表、还款记录表）动辄亿级，不加护栏一次慢查询就能拖垮整个服务

- [ ] **lifespan 启动零容错** ⚠️ 中优先级（发现于 L11 · 2026-04-30）
  - **位置**：`data-agent/app/core/lifespan.py` 第 17-21 行
  - **现状**：5 个 `init()` 裸调无 try/except，任一个失败 → 后面全停 → 服务启动失败
  - **风险场景**：
    1. Qdrant/ES 瞬时抽风（5-10s）→ init 失败 → 服务启动崩，pod 被 k8s 重启
    2. 前面 `embedding_client_manager.init()` 已成功 → 后面 qdrant 挂 → **embedding 连接泄漏**（无任何地方 close）
    3. 如果 logger 初始化更晚，**启动失败根因甚至没被记录**
  - **修复方案**（按依赖重要性选）：
    - **非致命依赖**（比如搜索增强）：try/except + 打告警日志 + 降级启动
    - **致命依赖**（比如主库）：`for attempt in range(3)` + 指数退避重试，失败才 raise
  - **credit-ops-agent 必补理由**：信贷系统容灾要求高，不能因定时维护导致 Qdrant 重启 30s 整个服务倒下

- [ ] **embedding_client 漏关闭** ⚠️ 低优先级（发现于 L11 · 2026-04-30）
  - **位置**：`data-agent/app/core/lifespan.py` 第 26-29 行
  - **现状**：启动阶段 5 个 init，关闭阶段只有 4 个 close——**embedding_client_manager 没 close**
  - **本质**：embedding_client 是 `HuggingFaceEndpointEmbeddings`（HTTP 客户端），内部持有 httpx/aiohttp AsyncClient + 连接池
  - **风险**：
    1. asyncio 退出时 `UserWarning: unclosed ClientSession`，日志干扰
    2. **SIGTERM 后进程可能挂起 10s**，docker stop 超时后 SIGKILL → 滚动更新时间放大
    3. 服务端 TCP 连接泄漏（高频重启时放大）
    4. **违反「资源申请-释放对称」契约**，同事读代码容易误以为是 bug
  - **修复方案**：在 `yield` 后面补 `await embedding_client_manager.close()`
  - **credit-ops-agent 必补理由**：建立 init/close 严格对称的约定，避免类似漏网

---

## [2026-05-08] U1 data-agent 精读阶段全毕业 🎓🎓🎓

**当前双轨状态**：

| 线 | 阶段 | 进度 |
|---|---|---|
| 理解线 | U1 data-agent | ✅ **全通关毕业**（L0-L12 + Final，共 14 关） |
| 理解线 | U2 knowledge_base RAG | ⏳ 下一阶段待开启 |

**今日完成**：L12 开放题深度讨论 + Final 口述补录通关，U1 阶段 13 个知识点全部内化。

### L12 · 迁移到 credit-ops 的设计（开放题）✅
- **Q1 核心洞察**：迁移主战场是 **meta_config.yaml（4 大 section：tables/columns/metrics/values）+ jieba userdict.txt**，代码 90% 不动 —— 「换业务 ≈ 换 yaml + 换词典」
- **术语映射 B+C 组合**（如「M1 → 逾期一月」）：
  - B：`ColumnInfo.description` 写语义解释 → Qdrant 字段向量语义兜底（用户说中文时）
  - C：`ValueInfo` 存枚举值 → ES 精确匹配（用户说 M1 时）
  - **不要新开 glossary section**（违反「复用现有三路召回架构」原则）
- **ValueInfo 设计边界**：只收「低基数 + 高命中概率 + 字面量匹配有意义」的字段值（如 M1/M2、城市名），高基数自由文本（评论）不收 —— 这才是 ES/Qdrant 分工的本质
- **jieba 认识业务黑话**：`jieba.load_userdict()` + 词典格式「词 词频 词性」+ **必须与 POS 白名单词性配对**（比如标 n、代码里 POS 白名单也放 n），否则穿透不到下游
- **Q2（RAG 场景复用）留白**：12 节点只 1 个原样复用（extract_keywords），6 个骨架可借，5 个废 —— 留待 U2 阶段真实体感后再补完

### Final · 合上文档 5 分钟口述（Mini 补录通关）✅
- **首录暴露两处硬错**：
  - 🚨 Repository 塞 state（**L1 倒退** —— 连接对象不可序列化，只能去 Context）
  - 🚨 元数据在 MySQL（**位置错** —— 元数据在 meta_config.yaml，MySQL 是业务库）
- **Mini-Final 三段补录**（三大考点 + 硬错修正）全部达标，因果链清晰：
  - **考点 ①** 三路召回 Qdrant/ES 分工：column/metric 是字段信息走语义（Qdrant）、value 是具体值走精确（ES）
  - **考点 ②** YAML 三参数：省 token（占用更少）、allow_unicode 防中文转义、sort_keys=False 保顺序（LLM 顺序敏感）
  - **考点 ③** EXPLAIN 前置校验不熔断 graph + 硬错软错（内部修不了就上抛、能修就降级）+ 有界重试三军规（硬上限 / 单向链路防循环 / 兜底兜住）

### 🎯 U1 阶段核心产出层级总结

**架构层**：五层架构 Web→Service→Agent(LangGraph)→Repository→Client + 12 节点 DAG

**机制层**：LangGraph Fan-out/Fan-in + 条件边 + Partial State Update + 双通道输出（return 给下游 / writer 吐前端）

**工程层**：State vs Context 分离 / 职责分离 / 两阶段检索 / 有界重试 / Fail Fast vs 降级 / init·close 对称 / 四层护栏

**协议层**：SSE（data:/\n\n）/ SQLAlchemy 2.0 类型 / stream_mode custom vs values

**提示工程**：YAML 顺序敏感（Lost in the Middle）/ 时间锚定 / Query Expansion / token-格式权衡

**迁移哲学**：元数据知识（meta_config.yaml + userdict.txt）才是业务护城河，代码复用度 90%+

### 📦 带入 U2 的资产
- **3 条生产护栏 Backlog**（execute_sql 零护栏 / lifespan 零容错 / embedding 漏关闭）持续累积
- **《我的质疑与思考》3 条模板**（待追加到 data-agent-architecture.md 末尾）：
  1. 迁移主战场是 meta_config.yaml + jieba userdict.txt，代码 90% 不动
  2. 词汇映射必须 B+C 组合（description 语义 + ValueInfo 枚举），不要新开 glossary
  3. ValueInfo 只收「低基数 + 高命中概率 + 字面量匹配有意义」的字段值

**下一步**：
- 切入 **U2 knowledge_base RAG 阶段**：打开 [kb-learning-journey.md](./kb-learning-journey.md)，新会话说「继续学习 RAG」即可
- 待补：在 [../learning/data-agent-architecture.md](../learning/data-agent-architecture.md) 末尾追加《我的质疑与思考》3 条（学员自己写）

---

## [2026-04-30 晚] L7-L11 五连通关 · data-agent 全场打通 🏆🏆🏆🏆🏆

**当前双轨状态**：

| 线 | 阶段 | 进度 |
|---|---|---|
| 理解线 | U1 data-agent | 🔄 L0-L11 全通，推进至 L12（收官开放题）|

**今晚完成**：一次连打 5 关 + 发现 3 条生产级 Backlog，data-agent 从召回筛选一路通到 SSE 生命周期。

### L7 · filter_table + filter_metric（LLM 当裁判）✅
- **两阶段检索**：召回宽进（limit=5, threshold=0.6）+ filter 严选；读写集不相交 → Fan-out 并行
- **list[:] 浅拷贝陷阱**：不是零拷贝，是为了防止边遍历边 remove 导致 index 左移跳元素
- **yaml.dump 三参数**：省 token、`allow_unicode=True` 防中文转义、`sort_keys=False` 保护人工编排顺序
- **关键顿悟**：LLM 对顺序极其敏感（Lost in the Middle / Position Bias）——sort_keys 重排会毁掉精心设计的 prompt 结构

### L8 · add_extra_context（动态上下文注入）✅
- **时间锚定问题**（Temporal Grounding Problem）：LLM 没有「现在」，只有「训练截止时间」
- **date_info 每次请求必刷**（日期一直在变）、**db_info 可考虑 lifespan 缓存**但会影响热变更 → 作者选择不缓存（拒绝紧耦合）
- **硬错 vs 软错**：方言错 = 语法错暴露快；日期错 = 结果错暴露慢（生产更可怕）

### L9 · SQL 自愈回路（generate + validate + correct）✅
- **职责分离**（SRP）：0→1 生成 / 裁判 / 1→1' 修复三个节点各管一档
- **业务错用 `return {"error": ...}`**（降级成数据，条件边走修复路径）；**系统错用 `raise`**（熔断交给上层 astream 兜底）
- **有界重试三军规**：硬上限（次数）+ 单向链路防循环（correct 不回走 validate）+ 兜底兜住（改不回来也不死循环）

### L10 · execute_sql + 结果回流 ✅
- **异常哲学 · 下游决定上游策略**：终点节点 Fail Fast 用 raise（没人兜底）；中间节点降级用 return error（有下游兜底）
- **双通道输出**：`return` 写 state 给下游节点，`writer` 吐给前端；`stream_mode="custom"` 只走 writer 通道
- **`text(sql)` 的本质**：把字符串包装成 SQLAlchemy `TextClause` 对象（2.0+ 强制），附带命名参数绑定能力（防 SQL 注入）
- **`RowMapping` 非 dict**：`dict(row)` 的三重价值——JSON 可序列化 / 切断 SQLAlchemy 依赖 / 解绑 session 生命周期
- **重大发现**：execute_sql **零护栏**——无 LIMIT 兜底 / 无流式 / 无超时 → 写入 Backlog #1。生产级应有「Prompt 软约束 + SQL 硬兜底 + 流式拉数据 + 超时强杀」四层护栏

### L11 · SSE 回流 + 生命周期 ✅
- **SSE 协议不是约定**：`data:` 是 W3C 字段标签（浏览器 EventSource 硬认），`\n\n` 是事件分隔符（空行 = 事件结束）
- **漏了最坑**：`\n\n` 漏成 `\n` → 浏览器缓冲区永远不触发 `.onmessage`，前端啥都收不到而且**无任何报错**（沉默失败）
- **stream_mode 选型**：`custom` 只吐 writer / `values` 吐完整 state 快照：带宽炸炉 + 内部细节泄露（id/SQL/错误栈）→ **生产禁用 values**
- **lifespan 两大坑**（写入 Backlog #2/#3）：
  - init 无 try → 部分成功+后续中断 → 资源泄漏 + 启动崩。生产写法：**重试 + 降级 + 快速失败**三选一
  - embedding_client 漏 close → unclosed 警告 + SIGTERM 挂起 10s + TCP 泄漏。**init/close 必须严格对称**

### 🎯 今晚核心产出层级总结

**机制层**（LangGraph 框架原理）：
- Fan-out/Fan-in 自动并发 + 条件边 / 双通道输出（return vs writer）

**工程层**（设计决策）：
- 职责分离 / 两阶段检索 / 有界重试 / Fail Fast vs 降级 / init·close 对称 / 四层护栏

**协议层**（外部规范）：
- SSE 协议（data: / \n\n）/ SQLAlchemy 2.0 类型系统 / stream_mode 各模式语义

**提示工程**（LLM 特性）：
- 顺序敏感（Lost in the Middle）/ 时间锚定 / 词汇不匹配（Query Expansion）/ token 与格式权衡

**生产护栏 Backlog**（至今 3 条）：
- #1 execute_sql 零护栏 · 高优 · L10
- #2 lifespan 启动零容错 · 中优 · L11
- #3 embedding_client 漏关闭 · 低优 · L11

### 🎁 今晚为简历/面试攻下的「金块素材」
- 「我学 data-agent 时发现 3 条生产级护栏缺失 + 补强方案」 → 写简历 / 面试聊天素材
- 「两个 Prompt 设计硬决策：sort_keys=False 护顺序 / allow_unicode=True 防转义」→ 对 LLM 敏感性的理解深度
- 「将 SQL 自愈归结为有界重试三军规」→ Agent 设计容错的理论框架

---

## [2026-04-30] L1-L6 六连通关 · data-agent 前半场打通 🏆🏆🏆

**当前双轨状态**：

| 线 | 阶段 | 进度 |
|---|---|---|
| 理解线 | U1 data-agent | 🔄 进行中（L0-L6 ✅ 通关，推进至 L7）|

**今日完成**：一天 7 关（含上午 L0），data-agent 从入口召回到上下文整形全部打通。

### L1 · State vs Context 分离 ✅
- **Entity vs State DTO** 按「存储位置 + 下游消费者」区分：Entity 在 `app/entities/`（给程序，带 id/外键），DTO 在 `app/agent/state.py`（给 LLM，纯语义字段）
- **Context 塞 State 会炸**：序列化/持久化不支持连接池、socket 这类运行时对象
- **DTO 去 id 三维度**：功能无用（LLM 不做关联查询）+ token 浪费 + id 信息暴露

### L2 · Graph 节点+边+条件路由 ✅
- **Fan-out**：同一起点 `add_edge` 到多个终点 → asyncio 自动并发（extract_keywords → 三路召回）
- **Fan-in**：多起点 `add_edge` 到同一终点 → super-step 模型自动等齐（三路召回 → merge_retrieved_info）
- **条件边**：`add_conditional_edges` + 路由函数 + 映射字典，运行时动态选路径（validate_sql → execute_sql / correct_sql）
- 画出完整 12 节点流水线拓扑图

### L3 · 关键词抽取 ✅
- **jieba 词性白名单**：12 种词性（n/nr/ns/v/...）过滤助词、时间词、数量词
- **非 LLM 决策**：第一个节点走纯规则，便宜快稳
- **LangGraph Partial State Update**：return dict 只替换命中字段，其他 9 个字段原样不动（非清空）
- 踩坑：误选 "最近" 保留（`t` 词性不在白名单）

### L4 · recall_column（LLM 扩写 + 向量召回）✅
- **LCEL 管道 `prompt | llm | parser`**：Python `__or__` 运算符重载，不是新语法
- **Query Expansion**：解决 Vocabulary Mismatch（用户词汇 ≠ 数据库词汇），经典 RAG 增强手段
- **逐词独立 embed**：保持语义单元粒度，避免多词平均化的语义漂移（戳破"拼串再 embed"的陷阱）
- **JsonOutputParser**：把 `AIMessage.content` 字符串自动 `json.loads`

### L5 · 三路召回对照（column/metric/value）✅
- **column/metric 用 Qdrant**：语义模糊匹配（"销售额" ≈ "营收" ≈ "gmv"）
- **value 用 ES**：离散枚举精确匹配（"北京"/"已支付" 就是那几个值）
- **选型原则**：有限离散值 + 精确匹配 → ES；自由文本 + 概念相关 → 向量库
- 新概念入库：**离散枚举**（Discrete Enumeration）

### L6 · merge_retrieved_info ✅
- **Q1 DTO 转换放最后一步**：前期全程 Entity，需 id 做字典 key、分表分组、主外键查重，第 86-89 行才转 DTO（呼应 L1）
- **Q2 主外键补全**：给 LLM 提供 **JOIN 锚点**（Schema Enrichment / Context Augmentation）——不补主外键会导致 SQL 笛卡尔积 / 幻觉字段 / 拒绝回答
- **Q3 retrieved_values 归宿追踪**：第 42-53 行融入 `column.examples`，去重追加，体现"数据归属关系必须在喂 LLM 前显式化"原则
- 113 行代码 0 次 LLM 调用，全是字典操作 + 数据库查询——**数据清洗在 AI 项目占大头**

**本次会话亮点**：
- 一天通过 7 关，节奏极快（6 小时闯完 data-agent 前半场）
- 学员全程主动贴选中代码段追问（merge_retrieved_info L40-41 定位精准）
- 概念抽象能力提升：从最初追问语法，到现在能独立回答工程决策题（Q2 主外键、Q3 数据归属）
- 建立了**设计原则清单**：DTO 转换放最后、为 LLM 显式补缺、数据归属关系显式化、数据清洗占大头

**下一步**：
- L7《filter_table + filter_metric》：第一次看"LLM 当裁判"模式（让 LLM 从召回结果里精选相关表/指标）
- 核心问题：为什么要再过一道 LLM？召回阶段已经过滤过一次了

---

## [2026-04-30] L0 架构俯瞰 · 闯关通关 🏆

**当前双轨状态**：

| 线 | 阶段 | 进度 |
|---|---|---|
| 理解线 | U1 data-agent | 🔄 进行中（L0 ✅ 通关，推进至 L1）|

**今日完成**：
- ✅ **L0 Q1 五层调用链**：Web→Router→Service→Agent(LangGraph)→Repository/Client
  - 关键文件：query_router.py / query_service.py / query_schema.py / context.py
  - 攻破 7 个语法盲点：APIRouter、Depends、async def、StreamingResponse、Pydantic BaseModel、yield 生成器、async for
  - 关键顿悟：**Depends 两条时间线**（定义时注册 vs 请求时执行）
  - 关键顿悟：**lifespan 生命周期线 vs 单次请求线**——客户端连接在 lifespan 建好，每次请求只是借用
- ✅ **L0 Q2 ContextVar 请求级变量隔离**
  - 纠正误区：ContextVar **不是**全局唯一变量，而是「一个变量名、每请求一份独立副本」
  - 底层机制：asyncio 给每个并发任务发独立的 Context 快照表，.set/.get 在各自表上操作
- ✅ **L0 Q3 lifespan 顶层反模式**
  - 三坑：import 副作用（CI 炸）/ 无优雅关闭（连接泄漏）/ 健康检查失真（k8s 无感知）
  - 学员判定：健康检查失真最致命（silent failure）

**本次会话亮点**：
- 学员主动贴选中代码段追问语法（query_router.py L10-18 / query_schema.py / query_service.py L40-45）
- 学员自发补刀双时间线，把 Repository 层和 lifespan 连起来讲
- learning-coach agent「单题精讲」机制在 L0 跑通，逐题深挖而非批量 Feedback

**下一步**：
- L1《State vs Context 分离》：打开 app/agent/state.py + app/agent/context.py
- 核心问题：State 和 Context 为什么要分开？Context 的 6 个仓储塞进 State 会怎样？

---

## [2026-04-29] 文档整合重组 · 双轨计划定稿

**当前双轨状态**：

| 线 | 阶段 | 进度 |
|---|---|---|
| 理解线 | U1 data-agent | ✅ 完成（learning-journey.md 已建，当前在 L0 待学员闯关）|
| 理解线 | U2 knowledge_base RAG | ⏳ 待开始（kb-learning-journey.md 已建）|
| 理解线 | U3 deep_search_pro | ⏳ 待开始（deep-search-learning-journey.md 已建）|
| 实操线 | R1 跑通 data-agent | ⏳ 待开始 |

**今日完成**：
- 整合 docs/ 文档体系，清理旧系统（ai-context.md 已重定向，docs/learning/progress.md 已归档）
- 重写 plan.md：3 项目 × 双轨（理解线 U1-U7 + 实操线 R1-R4），每个 Milestone 有明确验收标准
- 新建 kb-learning-journey.md（9 关，knowledge_base RAG 链路，基于 repowiki + RAGFlow 实践）
- 确认 knowledge_base 项目无独立代码，实操等价为跑通 RAGFlow（R2）
- 修复 learning-journey.md 所有 Windows 路径 → Mac 路径

**下一步**：
- 理解线：打开 kb-learning-journey.md，说"继续学习 RAG"，从 L0 开始
- 实操线：配置 data-agent .env，Docker 启动 MySQL + Qdrant + ES，完成 R1 验收

---

## [2026-04-29] 阶段 U1 学习方法框架落盘 · 闯关式问答

**当前阶段**：U1 data-agent 精读（AI 教材已产出，学员闯关进行中）

**今日完成**：
- 确定学习方法：闯关式问答（费曼 + 苏格拉底 + 主动回忆）
- 产出 learning-journey.md（13 关，完整 AI 接手协议 + 进度快照）
- 当前在 L0，3 个问题待学员自己读源码回答

**下一步**：学员读 main.py / lifespan.py / context.py，回答 L0 三个问题

---

## [2026-04-29] 阶段 U1 AI 代读完成 · data-agent 架构复盘

**今日完成**：
- 通读 data-agent 全部关键模块
- 产出 docs/learning/data-agent-architecture.md（架构图 + 时序图 + 三大面试考点 + 7 条实战坑）
- 关键收获：LangGraph 并行 + 条件路由 + 多路异构召回 + YAML 喂 LLM + 自愈机制本质是 reflection

**下一步**：进入 U2 knowledge_base RAG 精读

---

## [2026-04-28] 项目启动 · 计划落盘

**今日完成**：
- 确定转行目标：AI Agent 开发工程师
- 确定业务场景：信贷贷后智能运营助手
- 落盘初始文档：README.md + plan.md + progress.md

---

<!-- 模板：
## [YYYY-MM-DD] 一句话标题

**当前双轨状态**：（可选，变化时才写）

**今日完成**：
- ...

**卡点 / 疑问**：
- ...

**下一步**：
- ...
-->
