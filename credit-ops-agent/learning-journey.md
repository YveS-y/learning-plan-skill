# Credit Ops Agent · 学习旅程卡

> **单文件自包含**：任何 AI 读到本文件都能无缝接手陪学；学员自己更新进度。
> **当前阶段**：阶段 U1 — data-agent 源码精读（闯关式问答学习法）
> **配套文档**：
> - 知识地图：[../learning/data-agent-architecture.md](../learning/data-agent-architecture.md)（AI 已产出的复盘笔记，作为"教材"）
> - 进度日志：[./progress.md](./progress.md)（每日收工写一条）
> - 总计划：[./plan.md](./plan.md)

---

## 🤖 AI 接手协议（切换会话后必读）

**如果你是新会话中的 AI，接到类似"继续学习"、"接着推进"、"继续 data-agent"的指令，请按下面 6 步执行，不要跳步**：

1. **先读本文件完整一遍**，重点看 §4「当前进度快照」和 §5「13 关详情」
2. **锁定当前关卡**：从 §4 拿到 `IN_PROGRESS` 关卡编号（如 `L2`）
3. **读该关卡在 §5 的 Preview + Question**，并读 §4 里记录的「上次学员答案」和「上次遗留追问」
4. **按 §2 学习方法的 4 步循环**，决定当前该抛哪个问题：
   - 如果学员还没开始回答 → 复述该关卡的 3 个 Question
   - 如果学员答了一半 → 按 §3「Feedback 规范」点评 + 追问深层问题
   - 如果学员表示"过了" → 判定通关（按 §2 通关标准），更新 §4 进入下一关
5. **每次回复结束**，提醒学员在 §4 手动更新这 3 项：`当前关卡`、`上次提问`、`时间戳`
6. **严守 §3「AI 禁止事项」**：不要代读代码、不要直接给答案

> 💡 如果学员直接说"换个关卡/跳过/重做"，就按学员指令调整 §4，不要自作主张。

---

## 🧠 §1 学习方法：闯关式问答学习法

三种经典学习法融合：

| 方法 | 作用 |
|---|---|
| **费曼学习法** | 每关用自己的话讲出来，讲不顺=没懂 |
| **苏格拉底式问答** | AI 不给答案，只抛引导问题，靠追问深化 |
| **主动回忆 + SQ3R** | 带着问题读源码，读完合上文档复述 |

**三层递进结构**（每个概念都按此顺序过）：

| 层 | 问的问题 |
|---|---|
| **操作层** | 这个东西怎么用 / 代码怎么写？ |
| **理解层** | 为什么这样设计 / 背后的权衡是什么？ |
| **内化层** | 学员合上资料，用自己的话完整复述 |

**硬约束（AI 必须遵守）**：
- 每次只围绕 **1 个概念** 展开，多个概念强制拆成多轮
- 学员说"明白了" → 不直接放行，必须先让学员**复述一遍**（"用你自己的话说说看"）
- AI 连续输出超 **3 段** 前，必须先停下抛 1 个检查问题，确认学员跟上

---

## 🔄 §2 每关 4 步循环（SOP）

```
📍 Preview    AI 给定位（文档第 X 节 + 源码文件+行号，2-3 句话）
       ↓
❓ Question   AI 抛 1-3 个引导问题（不是考试题，是读码的钩子）
       ↓
🗣 Explain    学员读源码 + 用自己的话总结回答，发给 AI
       ↓
🔧 Feedback   AI 按 §3 规范纠错 + 追问 1 个深层问题
       ↓
   重复 Explain+Feedback，直到满足「通关标准」→ 进下一关
```

**Mini Feynman 检查点**：每过完 3-4 关，学员合上所有文档做 3 分钟口述复盘，AI 负责挑毛病。

**通关标准**（满足任一即可）：
- ✅ 学员的回答命中该关 Question 的核心因果链（见 §5 每关的「核心点」）
- ✅ 学员能对 AI 的追问给出合理回应（不必完美）
- ✅ 学员主动说"这关我懂了，过"

---

## 🤝 §3 AI 互动规范

### ✅ AI 该做的
- 按 §5 每关的「Question 种子」抛问题，但**可根据学员回答动态调整**
- 学员答对 → 用关键词确认（如"对，这里就是 fan-in"），然后追问 1 个更深的问题
- 学员答错 → **不直接给答案**，用反例/对比/源码某一行提示学员自己发现问题
- 学员答不完整 → 指出缺了什么维度，让其补充
- 每 3-4 关主动提出做 Mini Feynman
- **学员说"明白了"** → 回复"用你自己的话说说看"，听完复述再决定是否过关
- **每次只讲 1 个概念**，要展开多个概念时强制拆轮：讲完一个，确认学员跟上后再讲下一个
- **连续输出即将超 3 段** → 先停下，问 1 个检查问题（如"到这里，X 的作用是什么？"），收到回答再继续

### ❌ AI 禁止事项
- ❌ 不要代读源码后总结给学员（学员自己读）
- ❌ 不要把 Question 的标准答案直接贴出来
- ❌ 不要跨关卡"预热"下一关的内容
- ❌ 不要因为学员某处漏讲就自行补全——用追问让学员自己补
- ❌ 不要改动学员自己维护的 §4 进度记录（只能建议，学员自己写）

### 💬 Feedback 回复模板（推荐）
```
【打分】✅核心对 / ⚠️部分对 / ❌偏差大

【具体点评】
- 你讲对的：xxx（关键词确认）
- 你漏的：xxx 这个维度没讲到
- 你偏的：xxx 的因果链反了，提示你看 xx.py 第 X 行

【追问】（只问 1 个深层问题）
xxx？

【下一步建议】
继续补答 / 进入下一关 / 做 Mini Feynman
```

---

## 📍 §4 当前进度快照（⚠️ 学员每次收工自己更新此节）

```yaml
当前关卡: U1 已毕业 🎓（L12 + Final 全通关）
关卡名称: U1 data-agent 精读阶段 · 全通关，切入 U2 knowledge_base RAG
状态: PASSED
上次提问时间: 2026-05-08
上次 AI 提的问题: |
  U1 全关通过 → 切入 U2 阶段（knowledge_base RAG）
上次学员回答摘要: |
  L1 State vs Context 分离 ✅
    - Entity vs State DTO：存储位置区分（entities/ vs agent/state.py），按下游消费者（程序 vs LLM）定义
    - Context 塞 State 会炸：序列化/持久化不支持连接池/socket 对象
    - DTO 去 id 三维度：功能无用 + token 浪费 + id 信息暴露
  L2 Graph 节点+边+条件路由 ✅
    - Fan-out：同一起点 add_edge 到多终点 → asyncio 自动并发
    - Fan-in：多起点 add_edge 到同一终点 → super-step 模型自动等齐
    - 条件边：add_conditional_edges + 路由函数 + 映射字典
  L3 关键词抽取 ✅
    - jieba 词性白名单（n/nr/v/... 12 种），非 LLM 走纯规则
    - LangGraph Partial State Update：return dict 只替换命中字段，其他 9 个字段原样不动
  L4 recall_column ✅
    - LCEL 管道：prompt | llm | parser，是 Python 运算符重载（__or__），不是新语法
    - Query Expansion：解决 Vocabulary Mismatch（用户词汇≠数据库词汇）
    - 逐词独立 embed：保持语义单元粒度，避免平均化语义漂移
  L5 三路召回对照 ✅
    - column/metric 用 Qdrant（语义模糊匹配），value 用 ES（离散枚举精确匹配）
    - 数据特征决定选型：有限离散值+精确匹配 → ES；自由文本+概念相关 → 向量库
  L6 merge_retrieved_info ✅
    - DTO 转换放最后；主外键补全给 LLM 提供 JOIN 锚点；retrieved_values 融入 column.examples
  L7 filter_table + filter_metric（LLM 当裁判）✅
    - 两阶段检索：召回宽进 + filter 严选；Fan-out 并行（读写集不相交）
    - list[:] 浅拷贝（非零拷贝）+ 原列表 remove，避免边遍历边删 index 左移跳元素
    - yaml.dump 三参数：省 token、allow_unicode 防转义、sort_keys=False 护 Prompt 顺序
    - 关键顿悟：LLM 对顺序极其敏感（Lost in the Middle / Position Bias）
  L8 add_extra_context（动态上下文注入）✅
    - 时间锚定问题（Temporal Grounding）：LLM 没有"现在"，只有训练截止
    - date_info 每次请求必刷（硬错暴露快），db_info 可考虑 lifespan 缓存但会影响热变更
    - 硬错 vs 软错：方言错 = 语法错暴露快；日期错 = 结果错暴露慢（更可怕）
  L9 SQL 自愈回路（generate + validate + correct）✅
    - 职责分离：0→1 生成 / 裁判 / 1→1' 修复，条件边路由决定走向
    - 业务错用 return error（降级成数据，让图继续）；系统错用 raise（熔断给上层兜底）
    - 有界重试三军规：硬上限 / 单向链路防循环 / 兜底兜住（不能无限修）
  L10 execute_sql + 结果回流 ✅
    - 终点节点 Fail Fast（raise）vs 中间节点降级（return error）：下游决定上游策略
    - 双通道输出：return 写 state 给下游，writer 吐给前端，stream_mode="custom" 只走 writer
    - text(sql) 把字符串包成 SQLAlchemy SQL 对象（2.0+ 强制），附带防注入能力
    - RowMapping 非 dict，必须 dict(row) 转才能 json.dumps + 切断框架依赖 + 解绑 session 生命周期
    - 发现 execute_sql 零护栏 → 进 progress.md 常驻 Backlog
  L11 SSE 回流 + 生命周期 ✅
    - SSE 协议：data: 是 W3C 字段标签（不是约定）、\n\n 是事件分隔符（不是末尾惯例）；漏了浏览器沉默失败
    - stream_mode：custom 只吐 writer / values 吐完整 state 快照（带宽炸 + 内部泄漏 = 生产禁用）
    - init 无容错 → 资源泄漏 + 启动失败 + 抽风被放大；生产写法：重试+降级+快速失败三选一
    - embedding_client 漏 close → unclosed 警告 + SIGTERM 挂起 10s + TCP 泄漏 + 违反对称契约
    - 发现 lifespan 启动零容错 + embedding 漏关闭 → 进 Backlog（2/3 条）
  L12 迁移到 credit-ops 的设计（开放题）✅
    - 迁移主战场是 meta_config.yaml（4 大 section：tables/columns/metrics/values）+ jieba userdict.txt，代码 90% 不动
    - 术语映射 B+C 组合：ColumnInfo.description 写语义解释（Qdrant 语义兜底） + ValueInfo 存枚举值（ES 精确匹配）；不要新开 glossary
    - ValueInfo 边界原则：只收"低基数+高命中概率+字面量匹配有意义"的值，自由文本不碰
    - jieba 认识业务黑话靠 userdict.txt（词/词频/词性）+ POS 白名单配对（必须标为名词）
    - RAG 场景（Q2）12 节点只有 1 个原样复用（extract_keywords），6 个骨架可借，5 个废 → 留待 U2 真实体感后再补完
  Final · 合上文档 5 分钟口述（Mini 补录通关）✅
    - 首录暴露两处硬错：Repository 塞 state（L1 倒退）+ 元数据在 MySQL（位置错）
    - Mini-Final 三段补录（三大考点 + 硬错修正）全部达标，因果链清晰
    - 考点①三路召回 Qdrant/ES 分工：column/metric 走语义（Qdrant）、value 走精确（ES）
    - 考点②YAML 三参数：省 token、allow_unicode 防转义、sort_keys=False 保顺序（LLM 顺序敏感）
    - 考点③EXPLAIN 不熔断 + 硬错软错（上抛/降级）+ 有界重试三军规（硬上限/单向链路/兜底）
遗留追问: （U1 全关无遗留）
已通关:
  - L0 架构俯瞰 (2026-04-30)
  - L1 State vs Context 分离 (2026-04-30)
  - L2 Graph 节点+边+条件路由 (2026-04-30)
  - L3 关键词抽取 (2026-04-30)
  - L4 recall_column（LLM扩写+向量召回）(2026-04-30)
  - L5 三路召回对照 (2026-04-30)
  - L6 merge_retrieved_info (2026-04-30)
  - L7 filter_table + filter_metric（LLM 当裁判）(2026-04-30)
  - L8 add_extra_context（动态上下文注入）(2026-04-30)
  - L9 SQL 自愈回路（generate + validate + correct）(2026-04-30)
  - L10 execute_sql + 结果回流 (2026-04-30)
  - L11 SSE 回流 + 生命周期 (2026-04-30)
  - L12 迁移到 credit-ops 的设计（开放题）(2026-05-08)
  - Final 合上文档 5 分钟口述 · Mini 补录通关 (2026-05-08)
下一步:
  - 🎓 U1 data-agent 精读阶段全毕业
  - 切入 U2 knowledge_base RAG 阶段：打开 kb-learning-journey.md，新会话说"继续学习 RAG"即可
  - 待补：在 data-agent-architecture.md 末尾追加《我的质疑与思考》3 条（Q1 已备好模板）
Backlog 累积:
  - #1 execute_sql 零护栏 ⚠️ 高优先级（L10）
  - #2 lifespan 启动零容错 ⚠️ 中优先级（L11）
  - #3 embedding_client 漏关闭 ⚠️ 低优先级（L11）
```

> 💡 **学员更新指引**：每次会话结束前，把上面 YAML 里的字段手动改一下（5 行内即可），切会话时 AI 只看这一节就能接上。

---

## 📋 §5 13 关详情（含 Preview + Question 种子 + 通关标准）

> **标注说明**：⭐ = 重点关卡（对应面试三大考点）｜ ⏱ 预估耗时

### ✅ 通关记录（学员自更新）

- [x] **L0** 架构俯瞰 ⏱10min ✅ 2026-04-30
- [x] **L1** State vs Context 分离 ⏱15min ✅ 2026-04-30
- [x] **L2** Graph 节点+边+条件路由 ⏱15min ✅ 2026-04-30
- [x] **L3** 关键词抽取 ⏱10min ✅ 2026-04-30
- [x] **L4** ⭐ 三路召回的共性与差异 ⏱25min ✅ 2026-04-30
- [x] **L5** 多路融合与主外键补齐 ⏱15min ✅ 2026-04-30
- [x] **L6** LLM 二级精选 ⏱15min ✅ 2026-04-30
- [x] **L7** filter_table + filter_metric（LLM 当裁判）⏱10min ✅ 2026-04-30
- [x] **L8** ⭐ add_extra_context（动态上下文注入）⏱10min ✅ 2026-04-30
- [x] **L9** SQL 自愈回路（generate + validate + correct）⏱15min ✅ 2026-04-30
- [x] **L10** ⭐ execute_sql + 结果回流 ⏱15min ✅ 2026-04-30
- [x] **L11** SSE 回流 + 生命周期 ⏱10min ✅ 2026-04-30
- [x] **L12** 迁移到 credit-ops 的设计 ⏱20min ✅ 2026-05-08
- [x] **Final** 合上文档 5 分钟口述 ⏱10min ✅ 2026-05-08（Mini 补录通关）

---

### L0 · 架构俯瞰 ⏱10min

**📍 Preview**
data-agent 是五层架构：Web → Service → Agent(LangGraph) → Repository → Client。入口 [main.py](file:///Users/ys/code/ai_agent/data-agent/main.py) 只有 68 行，配套读 [lifespan.py](file:///Users/ys/code/ai_agent/data-agent/app/core/lifespan.py) + [core/context.py](file:///Users/ys/code/ai_agent/data-agent/app/core/context.py)。对应笔记 §1-§3。

**❓ Question 种子**
1. 一个 POST 请求从进来到拿到结果，经过哪几层？
2. `main.py` 中间件里为什么要生成 `request_id`？不生成会怎样？
3. `lifespan` 的作用是什么？如果去掉它、把 5 个 `init()` 放到 `main.py` 顶层，会出什么问题？

**🎯 核心点（AI 验收标准）**
- 能说出至少 4 层（Middleware/Router/Service/Graph）
- 理解 `request_id` 是用 ContextVar 做**请求级日志追踪**
- 知道 `lifespan` 是 FastAPI 的**启动/关闭钩子**，放在顶层会导致连接池在 import 阶段就建立，不受 app 生命周期管理

---

### L1 · State vs Context 分离 ⏱15min

**📍 Preview**
LangGraph 把"数据"和"依赖"分开存：`DataAgentState` 存业务数据，`DataAgentContext` 存外部客户端/仓储。对应笔记 §4.1。

**🎯 源码**：[state.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/state.py#L1-L59) + [context.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/context.py#L1-L19)

**❓ Question 种子**
1. State 里为什么同时有 `retrieved_columns: list[ColumnInfo]` 和 `table_infos: list[TableInfoState]`？为什么不统一一个？
2. 如果把 Context 里的 6 个仓储对象**塞进 State**，会出什么问题？（提示：state 会被持久化/序列化）
3. `ColumnInfo` 比 `ColumnInfoState` 多出来的字段是什么？为什么喂 LLM 要去掉？

**🎯 核心点**
- Entity(`ColumnInfo`) 是带 ID/外键的**内部表示**；State(`ColumnInfoState`) 是给 LLM 的**语义 DTO**
- State 会跨节点传递/可被持久化，放 Repository 这种带连接的对象不可序列化
- 去掉 ID 节省 token + 防泄漏内部主键

---

### L2 · Graph 节点+边+条件路由 ⏱15min

**📍 Preview**
整个工作流由 `StateGraph` 组装的 12 节点 DAG，含三路并行 + 双精选并行 + 1 个条件路由。对应笔记 §4.2。

**🎯 源码**：[graph.py L30-L68](file:///Users/ys/code/ai_agent/data-agent/app/agent/graph.py#L30-L68)

**❓ Question 种子**
1. `recall_column / recall_value / recall_metric` 怎么就自动并行了？哪几行代码决定的？
2. `merge_retrieved_info` 为什么一定要所有三路完成才执行？LangGraph 怎么知道等齐？
3. 那个条件路由的 lambda 如果 `state["error"]` 从未被设置过会怎样？

**🎯 核心点**
- 并行由 **同一个父节点 add_edge 到多个子节点 + 多个子节点 add_edge 到同一个汇聚节点** 实现（fan-out/fan-in）
- LangGraph 按 DAG 依赖自动等齐（barrier）
- TypedDict 未赋值的 key 取时会 KeyError，所以 validate_sql 节点**必须显式 return `{"error": None}`**

---

### L3 · 关键词抽取 ⏱10min

**📍 Preview**：整个 pipeline 起点，用 jieba POS 过滤。对应笔记 §5 节点1。

**🎯 源码**：[extract_keywords.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/extract_keywords.py)

**❓ Question 种子**
1. 为什么要限制 POS（n/nr/ns/vn...）？不限会怎样？
2. 最后 `keywords + [query]` 把原始问题也加进去，为什么？
3. 这一层用 jieba 不用 LLM，好处是什么？

**🎯 核心点**
- 不限 POS 会把"的/了/是"这类停用词喂下游，污染召回
- 保底：LLM 扩展失败时还有基础关键词
- jieba 快/免费/可解释，LLM 贵/慢/易漂

---

### L4 · ⭐ 三路召回的共性与差异 ⏱25min

**📍 Preview**：三个节点代码模式几乎一样，差异藏在 2 个地方。对应笔记 §6 考点一前半。

**🎯 源码**（必须对比着看）：
- [recall_column.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/recall_column.py)
- [recall_value.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/recall_value.py)
- [recall_metric.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/recall_metric.py)
- 3 个 prompt：`extend_keywords_for_{column,value,metric}_recall.prompt`

**❓ Question 种子**
1. 三个节点的**差异只在哪 2 处**？
2. 为什么字段/指标用 Qdrant 向量、字段值用 ElasticSearch BM25？**能不能都用向量**？
3. 三个扩展 Prompt 的"禁止输出"分别是什么？为什么要分 3 个 Prompt 不合成 1 个？

**🎯 核心点**
- 差异 1：调用的仓储不同（Qdrant 字段/Qdrant 指标/ES 值）
- 差异 2：向量走 `embedding_client.aembed_query + repo.search(vec)`，ES 直接 `repo.search(keyword)`
- 字段值是离散字面量（"上海"/"已支付"），向量会把"北京"也召回高分，BM25 精确匹配更准
- 3 个 Prompt 各司其职避免发散，角色专一度越高输出越稳

---

### L5 · 多路融合与主外键补齐 ⏱15min

**📍 Preview**：三路结果汇聚，补齐主外键和表元数据，转为 State DTO。对应笔记 §6 考点一后半。

**🎯 源码**：[merge_retrieved_info.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/merge_retrieved_info.py)

**❓ Question 种子**
1. 这个节点里 4 个 for 循环各做什么？去掉**哪一个**最可能导致 SQL 写不出来？
2. 为什么要显式补主外键？如果只用召回的字段写 SQL 会发生什么？
3. 最后为什么要把 `ColumnInfo` 转成 `ColumnInfoState`？（关联 L1）

**🎯 核心点**
- 循环 1：指标依赖的字段补齐；2：值合并到 examples；3：按表分组；4：补主外键
- 去掉主外键补齐 → LLM 写不出 JOIN（找不到关联键）
- 转 State 回收 L1 的设计：给 LLM 的语义 DTO

---

### L6 · LLM 二级精选 ⏱15min

**📍 Preview**：召回"宽"（threshold=0.6/limit=5），这里"严"（LLM 挑）。对应笔记 §5 节点6-7。

**🎯 源码**：[filter_table.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/filter_table.py) + [filter_metric.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/filter_metric.py) + 两个 `filter_*.prompt`

**❓ Question 种子**
1. 两个 filter 节点**并行**执行，如果让它们串行会有什么影响？
2. `JsonOutputParser` 保证什么？LLM 输出不合法 JSON 会怎样？
3. `yaml.dump(..., allow_unicode=True, sort_keys=False)` 三个参数各防什么坑？

**🎯 核心点**
- 两者无数据依赖，并行省一半时间
- Parser 失败会抛异常被 try/except 捕获 + SSE 推 error
- allow_unicode=True 防中文转义；sort_keys=False 保留人写的顺序（LLM 对顺序敏感）

---

### L7 · 动态上下文注入 ⏱10min

**📍 Preview**：补日期和 DB 方言，让 LLM 理解"昨天"/"上个月"这类相对时间。对应笔记 §5 节点8。

**🎯 源码**：[add_extra_context.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/add_extra_context.py)

**❓ Question 种子**
1. 为什么日期/DB 信息不放在请求入参里、要在节点里动态拿？
2. 如果 LLM 拿不到 `db_info.dialect`，可能会生成什么错误 SQL？
3. `quarter` 为什么要单独算而不是让 LLM 自己算？

**🎯 核心点**
- 请求侧感知不到服务器时区/DB 方言，动态拿才准
- 会生成 PG/MSSQL 函数（NOW() vs GETDATE() vs SYSDATE）
- LLM 算季度容易错且不稳定，工程侧算一次零成本

---

### L8 · ⭐ SQL 生成 Prompt 设计 ⏱20min

**📍 Preview**：Prompt = 角色 + 上下文（4 段 YAML）+ 6 条硬规则 + 用户问题。对应笔记 §6 考点二。

**🎯 源码**：[generate_sql.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/generate_sql.py) + [generate_sql.prompt](file:///Users/ys/code/ai_agent/data-agent/prompts/generate_sql.prompt) + [meta_config.yaml](file:///Users/ys/code/ai_agent/data-agent/conf/meta_config.yaml)

**❓ Question 种子**
1. 为什么用 YAML 不用 JSON 喂 LLM？能省多少 token？
2. 上下文分 4 段（table/metric/date/db）而不是塞一起，好处是什么？
3. Prompt 里 6 条硬规则挑 1 条拿掉，最可能出什么 bug？

**🎯 核心点**
- YAML 省 20-30% token（无引号、无逗号），缩进结构 LLM 敏感
- 分段 = 职责清晰，后续可按段替换/缓存
- 比如去掉"仅 SELECT" → LLM 可能生成 DELETE

---

### L9 · 验证 + 条件路由 ⏱10min

**📍 Preview**：用 EXPLAIN 做前置校验，失败走自愈。对应笔记 §6 考点三前半。

**🎯 源码**：[validate_sql.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/validate_sql.py) + [graph.py L61-L63](file:///Users/ys/code/ai_agent/data-agent/app/agent/graph.py#L61-L63)

**❓ Question 种子**
1. 为什么用 EXPLAIN 而不是真的执行一次看有没有报错？
2. 条件路由的 lambda 如果写成 `if state.get("error")` 会怎样？（微妙的 bug）
3. `validate_sql` 成功时必须显式 `return {"error": None}` 为什么不能省？

**🎯 核心点**
- EXPLAIN 无副作用、快、只校语法+表字段存在性
- `state.get("error")` 在 error=空字符串时也会走成功分支——数据库某些错误信息可能为空
- TypedDict 不存在的 key 取时 KeyError，必须显式 set

---

### L10 · ⭐ SQL 自愈：两个 Prompt 对比 ⏱15min

**📍 Preview**：`correct_sql.prompt` 比 `generate_sql.prompt` 多了 3 条约束。对应笔记 §6 考点三后半 + §9 坑 1。

**🎯 源码**：[correct_sql.py](file:///Users/ys/code/ai_agent/data-agent/app/agent/nodes/correct_sql.py) + [correct_sql.prompt](file:///Users/ys/code/ai_agent/data-agent/prompts/correct_sql.prompt)

**❓ Question 种子**
1. 对比两个 prompt，correct 版多了哪 3 条约束？为什么需要这几条？
2. 当前自愈只修一次（correct → execute），能不能循环修正？代价是什么？
3. `correct_sql.py` L27 的 `input_variables=["query", "metric_infos"]` 其实有个小问题，你看出来了吗？

**🎯 核心点**
- 多了：基于 error 修正 / 保持原业务语义 / 最小必要修改——都是防"越改越错"
- 循环的风险：死循环 + 成本 + 延迟；收益递减（多半改不回来）
- L27 的 input_variables 不完整（实际用了 7 个变量）——Python 不强校验所以能跑，但不规范，是可写进简历的"我发现并修复了……"的小细节

---

### L11 · SSE 回流 + 生命周期 ⏱10min

**📍 Preview**：节点用 `stream_writer` 推进度，Service 用 SSE 格式化；`lifespan` 管连接池。对应笔记 §7。

**🎯 源码**：[query_service.py L30-L44](file:///Users/ys/code/ai_agent/data-agent/app/services/query_service.py#L30-L44) + [lifespan.py](file:///Users/ys/code/ai_agent/data-agent/app/core/lifespan.py)

**❓ Question 种子**
1. `graph.astream(..., stream_mode="custom")` 的 "custom" 是什么意思？和默认的 "values" 有啥区别？
2. SSE 格式为什么是 `data: xxx\n\n`？前端怎么解析？
3. 如果 qdrant 挂了，lifespan.close() 里会出什么问题？

**🎯 核心点**
- "custom" = 节点里 writer 推啥 yield 啥（即前端看到的 progress）；"values" = 每个节点结束推整个 state（太冗余）
- SSE 规范：`data:` 前缀 + 双 `\n` 为一个事件
- qdrant 挂了 client 变 None，close() 抛 AttributeError——是生产环境需要加 try 的坑

---

### L12 · 迁移到 credit-ops 的设计 ⏱20min

**📍 Preview**：把 data-agent 的经验落地到信贷贷后场景。对应笔记 §8 + §9。

**❓ Question 种子（开放）**
1. 信贷贷后"M1 逾期人群 TOP5 渠道"这个问题，data-agent 的 pipeline 能直接跑吗？要改哪几层？
2. "合规话术边界"这种文档问答场景，data-agent 的 12 节点有几个可以复用？
3. §9 的 7 条坑里，挑 2 条你觉得**最值得在 credit-ops 里提前规避**的，为什么？

**🎯 核心点**（无标准答案，学员自由发挥）
- meta_config.yaml 换成信贷的表和指标
- 扩展词 Prompt 增加"催收/风险/额度"等业务维度
- RAG 场景（合规话术）复用不了 12 节点，要新搭一个子图（下阶段 1-2）
- 坑 2（无重试上限）+ 坑 7（SQL 白名单）是最值得提前加的

**💡 这关结束后**：在 [data-agent-architecture.md](../learning/data-agent-architecture.md) 末尾追加 **「我的质疑与思考」** 小节（>= 3 条自己写的观点）

---

### Final · 合上文档 5 分钟口述 ⏱10min

**动作**：合上所有源码和文档，对着手机录音 5 分钟讲完以下内容：
1. data-agent 整体架构（60s）
2. 一次问数请求的完整链路（90s）
3. 三大面试考点各 60s
4. 迁移到 credit-ops 的关键取舍（30s）

**通关标准**：录完自己听一遍，**不卡壳、不看稿**、术语准确。

---

## 🔗 §6 关联文档

| 文件 | 作用 |
|---|---|
| [../learning/data-agent-architecture.md](../learning/data-agent-architecture.md) | 知识地图（AI 产出的"教材"，读源码对照用） |
| [./plan.md](./plan.md) | 45 天总计划（阶段 1-1 是当前位） |
| [./progress.md](./progress.md) | 日志（每次收工追加一条） |
| [./README.md](./README.md) | 项目一页纸总览 |

---

## 📝 §7 学员使用提示

1. **每次开新会话**：把这个文件 @ 给 AI，说"继续学习"即可
2. **每次收工**：更新 §4 当前进度快照的 `当前关卡` / `上次提问` / `时间戳` 三个字段（30 秒）
3. **每过一关**：在 §5 「通关记录」勾选对应的 `- [ ]` → `- [x]`
4. **过完 3-4 关**：主动跟 AI 说"做 Mini Feynman"触发口述检查
5. **全部通关后**：在 [progress.md](./progress.md) 追加一条"阶段 1-1 彻底收官"日志，切换到阶段 1-2

> 💬 遇到 AI 不按规范执行（代读代码/直接给答案）→ 引用本文件 §3 禁止事项提醒。
