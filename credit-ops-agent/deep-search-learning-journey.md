# Credit Ops Agent · deep_search_pro 学习旅程卡

> **单文件自包含**：任何 AI 读到本文件都能无缝接手陪学；学员自己更新进度。
> **当前阶段**：阶段 U3 — deep_search_pro 源码精读（闯关式问答学习法）
> **配套文档**：
> - 知识地图：`../learning/deep-search-pro-architecture.md`（阶段 1-2 交付物，精读后产出）
> - 进度日志：[./progress.md](./progress.md)
> - 总计划：[./plan.md](./plan.md)

---

## 🤖 AI 接手协议（切换会话后必读）

**如果你是新会话中的 AI，接到类似"继续学习"、"继续 deep_search_pro"的指令，请按下面 6 步执行，不要跳步**：

1. **先读本文件完整一遍**，重点看 §4「当前进度快照」和 §5「10 关详情」
2. **锁定当前关卡**：从 §4 拿到 `IN_PROGRESS` 关卡编号（如 `L2`）
3. **读该关卡在 §5 的 Preview + Question**，并读 §4 里记录的「上次学员答案」和「上次遗留追问」
4. **按 §2 学习方法的 4 步循环**，决定当前该抛哪个问题：
   - 如果学员还没开始回答 → 复述该关卡的 Question
   - 如果学员答了一半 → 按 §3「Feedback 规范」点评 + 追问深层问题
   - 如果学员表示"过了" → 判定通关，更新 §4 进入下一关
5. **每次回复结束**，提醒学员在 §4 手动更新：`当前关卡`、`上次提问`、`时间戳`
6. **严守 §3「AI 禁止事项」**：不要代读代码、不要直接给答案

> 💡 如果学员直接说"换个关卡/跳过/重做"，就按学员指令调整 §4，不要自作主张。

---

## 🧠 §1 学习方法：闯关式问答学习法

与 data-agent 学习旅程相同（费曼 + 苏格拉底 + 主动回忆），见 [learning-journey.md §1](./learning-journey.md)。

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
📍 Preview    AI 给定位（源码文件+行号，2-3 句话）
       ↓
❓ Question   AI 抛 1-3 个引导问题
       ↓
🗣 Explain    学员读源码 + 用自己的话总结回答
       ↓
🔧 Feedback   AI 纠错 + 追问 1 个深层问题
       ↓
   重复直到满足「通关标准」→ 进下一关
```

**Mini Feynman 检查点**：每过完 3-4 关，合上文档口述 3 分钟，AI 挑毛病。

**通关标准**（满足任一即可）：
- ✅ 回答命中该关 Question 的核心因果链
- ✅ 能对 AI 的追问给出合理回应（不必完美）
- ✅ 学员主动说"这关我懂了，过"

---

## 🤝 §3 AI 互动规范

### ✅ AI 该做的
- 按 §5 每关的 Question 种子抛问题，但可根据学员回答动态调整
- 学员答对 → 用关键词确认，然后追问 1 个更深的问题
- 学员答错 → 不直接给答案，用反例/对比/源码某一行提示
- **学员说"明白了"** → 回复"用你自己的话说说看"，听完复述再决定是否过关
- **每次只讲 1 个概念**，要展开多个概念时强制拆轮
- **连续输出即将超 3 段** → 先停下，问 1 个检查问题，收到回答再继续
- 每 3-4 关主动提出做 Mini Feynman

### ❌ AI 禁止事项
- ❌ 不要代读源码后总结给学员
- ❌ 不要把 Question 的标准答案直接贴出来
- ❌ 不要跨关卡"预热"下一关的内容
- ❌ 不要改动学员自己维护的 §4 进度记录

### 💬 Feedback 回复模板（推荐）
```
【打分】✅核心对 / ⚠️部分对 / ❌偏差大

【具体点评】
- 你讲对的：xxx
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
当前关卡: L0
关卡名称: 架构俯瞰
状态: NOT_STARTED   # 可选值：NOT_STARTED / IN_PROGRESS / PASSED
上次提问时间: （未开始）
上次 AI 提的问题: （无）
上次学员回答摘要: （尚未回答）
遗留追问: （无）
下一步:
  - 学员打开 api/server.py + agent/main_agent.py，读完后回答 L0 的 3 个问题
```

---

## 📋 §5 10 关详情（含 Preview + Question 种子 + 通关标准）

> **标注说明**：⭐ = 重点关卡（对应面试三大考点）｜ ⏱ 预估耗时

### ✅ 通关记录（学员自更新）

- [ ] **L0** 架构俯瞰 ⏱10min
- [ ] **L1** ⭐ create_deep_agent vs LangGraph ⏱15min
- [ ] **L2** 子智能体定义方式 ⏱10min
- [ ] **L3** Prompt 设计 ⏱10min
- [ ] **L4** ⭐ 工具设计模式 ⏱20min
- [ ] **L5** ⭐ RAGFlow 集成 ⏱20min
- [ ] **L6** ⭐ WebSocket 实时通信 ⏱20min
- [ ] **L7** ContextVar 会话隔离 ⏱10min
- [ ] **L8** 文件管理机制 ⏱10min
- [ ] **L9** 迁移到 credit-ops 的设计 ⏱20min
- [ ] **Final** 合上文档 5 分钟口述 ⏱10min

---

### L0 · 架构俯瞰 ⏱10min

**📍 Preview**
整个项目是两层：`api/`（HTTP + WebSocket 接口层）+ `agent/`（Main Agent + 3 Sub Agents）。入口读 [api/server.py](file:///Users/ys/code/ai_agent/deep_search_pro/api/server.py) 和 [agent/main_agent.py](file:///Users/ys/code/ai_agent/deep_search_pro/agent/main_agent.py)（仅 L26-L36 的创建部分）。

**❓ Question 种子**
1. 一个前端请求从进来到拿到结果，经过哪几层？（对比 data-agent 的五层架构，这里是几层？）
2. `POST /api/task` 返回的是最终结果吗？如果不是，结果通过什么通道回来？
3. `@app.on_event("startup")` 里为什么要 `manager.set_loop(loop)`？不设置会怎样？

**🎯 核心点（AI 验收标准）**
- 能说出：HTTP POST → `asyncio.create_task` 后台异步运行 Agent → 结果通过 WebSocket 推回
- 知道响应是立即返回 `{"status": "started", "thread_id": "..."}` 而不是等 Agent 跑完
- 理解 `set_loop` 是把 FastAPI 的事件循环绑到 WebSocket 管理器，避免跨线程投递 coroutine 时 loop 不一致

---

### L1 · ⭐ create_deep_agent vs LangGraph ⏱15min

**📍 Preview**
`main_agent.py` 用的是 `from deepagents import create_deep_agent`，而不是 data-agent 的 `StateGraph`。这是面试最高频的对比题。读 [agent/main_agent.py L26-L36](file:///Users/ys/code/ai_agent/deep_search_pro/agent/main_agent.py)。

**❓ Question 种子**
1. `create_deep_agent` 接收 `subagents=[]` 参数，而 LangGraph 的子图是通过 `add_node` 编入的——两种方式的本质区别是什么？（从"谁来决定调哪个"角度思考）
2. `checkpointer=InMemorySaver()` 的作用是什么？去掉它会失去什么能力？
3. 用 `create_deep_agent` 你获得了什么便利？又放弃了什么控制力？

**🎯 核心点**
- `create_deep_agent`：LLM 自主决定调哪个 subagent（工具调用驱动路由）；LangGraph StateGraph：开发者显式定义每条边和路由条件（确定性更强）
- `InMemorySaver`：给 Agent 提供对话历史记忆（checkpointing），去掉则每次调用无记忆，无法做多轮追问
- 便利：少写 graph 结构代码；放弃：精细的条件路由、状态可视化、并行节点控制

---

### L2 · 子智能体定义方式 ⏱10min

**📍 Preview**
三个 subagent（kb / db / network）都是纯 dict，不是 class 也不是 compiled graph。读 [agent/subagents/knowledge_base_agent.py](file:///Users/ys/code/ai_agent/deep_search_pro/agent/subagents/knowledge_base_agent.py) + 另外两个同名文件。

**❓ Question 种子**
1. subagent dict 里有哪几个 key？每个 key 的作用是什么？
2. `description` 字段对 LLM 路由有多重要？如果两个 subagent 的 description 高度相似会发生什么？
3. knowledge_base_agent 只有 2 个工具，而 data-agent 的 SQL 链路有 12 个节点——这两种设计各自的适用场景是什么？

**🎯 核心点**
- dict 有 `name / description / system_prompt / tools` 四个 key；`description` 是 LLM 路由的依据——主 Agent 看它决定"该问谁"
- description 相似 → LLM 路由混乱，是生产环境的真实 bug 点
- dict + 工具适合"靠 LLM 推理决策"的场景；StateGraph 适合"流程确定、需要并行/条件路由"的场景

---

### L3 · Prompt 设计 ⏱10min

**📍 Preview**
Prompt 存在两处：`agent/prompts.py`（Python dict，运行时加载）和 `prompt/prompts.yml`（YAML 配置）。读 [agent/prompts.py](file:///Users/ys/code/ai_agent/deep_search_pro/agent/prompts.py) + [prompt/prompts.yml](file:///Users/ys/code/ai_agent/deep_search_pro/prompt/prompts.yml)。

**❓ Question 种子**
1. `prompts.py` 和 `prompts.yml` 的职责分工是什么？为什么要分两处？
2. main_agent 的 system_prompt 里最重要的一条约束是什么？去掉它最可能出什么问题？
3. 对比 data-agent 的 `prompts/*.prompt` 文件方案和这里的 YAML 方案，各有什么优劣？

**🎯 核心点**
- `.yml` 存配置（多语言/多环境可替换），`prompts.py` 做加载和数据访问（统一入口）
- main_agent 的关键约束是"必须委托 subagent 而非自行回答"——去掉后 LLM 会绕过工具直接生成答案
- `.prompt` 文件对非开发者友好（运营可直接改），YAML 结构化易解析但不如纯文本直观

---

### L4 · ⭐ 工具设计模式 ⏱20min

**📍 Preview**
`tools/` 下有 6 个工具文件：`db_tools / markdown_tools / pdf_tools / ragflow_tools / tavily_tool / upload_file_read_tool`。对比着读，找共性和差异。

**❓ Question 种子**
1. 这 6 个工具在**结构上**有哪些共性？（装饰器、埋点、异常处理）
2. `monitor.report_tool(tool_name=...)` 这行"埋点"的作用是什么？如果去掉，用户会有什么感知变化？
3. 工具的 docstring 对 LLM 的意义是什么？`create_ask_delete` 的 docstring 里有一句关键约束，你看出来了吗？

**🎯 核心点**
- 共性：`@tool` 装饰器（LangChain 工具注册）+ `monitor.report_tool` 埋点 + `try/except` 返回字符串错误而非抛异常
- 埋点去掉 → WebSocket 前端看不到工具调用进度，用户体验从"实时感知"降为"黑盒等待"
- docstring 是 LLM 判断"该不该调用此工具"的依据；`create_ask_delete` 里的关键约束是"调用前必须先调用 `get_assistant_list`"——这是防止 LLM 猜名字乱调用的工程护栏

---

### L5 · ⭐ RAGFlow 集成 ⏱20min

**📍 Preview**
RAGFlow 是外部 RAG 服务，通过 HTTP API 接入，不是本地向量库。读 [rawflow/knowledge_demo.py](file:///Users/ys/code/ai_agent/deep_search_pro/rawflow/knowledge_demo.py) + [tools/ragflow_tools.py](file:///Users/ys/code/ai_agent/deep_search_pro/tools/ragflow_tools.py)。

**❓ Question 种子**
1. 使用 RAGFlow 和 data-agent 里"自建 Qdrant + ES"相比，各有什么优劣？
2. `create_ask_delete` 里为什么要"创建会话 → 提问 → 立即删除会话"，而不是复用一个长期会话？
3. RAGFlow 的聊天助手和知识库是分开的两层概念——LLM 工具调用时为什么先查助手列表而不是直接查知识库？

**🎯 核心点**
- RAGFlow 优势：文档解析/切片/向量化全部外包，省去大量工程量；劣势：依赖外部服务、切片参数不可控、本地化部署成本高
- 删除会话：防止会话历史污染下一次查询，实现"一次性查询"语义，避免 RAGFlow 端会话数据膨胀
- RAGFlow 架构：知识库（数据）+ 聊天助手（配置了用哪些知识库+Prompt 的推理实体）；LLM 只知道"哪个助手能回答什么"，通过 description 匹配，不直接操作知识库

---

### L6 · ⭐ WebSocket 实时通信 ⏱20min

**📍 Preview**
data-agent 用 SSE，deep_search_pro 用 WebSocket。读 [api/server.py L195-L239](file:///Users/ys/code/ai_agent/deep_search_pro/api/server.py) + [api/monitor.py](file:///Users/ys/code/ai_agent/deep_search_pro/api/monitor.py)。

**❓ Question 种子**
1. 为什么这个项目选 WebSocket 而不是 SSE？结合 `monitor._emit` 的两条发送路径想想看
2. `monitor._emit` 里有一段跨线程安全的处理逻辑（`current_loop == manager_loop` 的判断），为什么需要这个？不加会出什么问题？
3. `ToolMonitor` 用了单例模式（`__new__`）——为什么工具监控要用单例？如果每个请求创建一个实例会怎样？

**🎯 核心点**
- WebSocket 是双向长连接，可以从服务端主动推送任意时刻的中间状态（工具调用、子智能体调用）；SSE 是单向只读流，适合线性输出；这里需要推送多种事件类型（tool_start/assistant_call/task_result），WebSocket 更合适
- `run_deep_agent` 在 `asyncio.create_task` 的协程里，`monitor` 的 `_emit` 可能被同步线程或不同协程调用；不区分 loop 直接 `create_task` 会 RuntimeError（"This event loop is already running" 或 "attached to a different loop"）
- 单例确保所有工具共享同一个 `websocket_manager`；多实例则新建的实例没有 `websocket_manager`，消息发不出去

---

### L7 · ContextVar 会话隔离 ⏱10min

**📍 Preview**
data-agent 和 deep_search_pro 都用了 `ContextVar` 做请求级隔离，但隔离的内容不同。读 [api/context.py](file:///Users/ys/code/ai_agent/deep_search_pro/api/context.py) + data-agent 的 `app/core/context.py` 对比。

**❓ Question 种子**
1. deep_search_pro 的 `context.py` 里存了哪两个变量？分别被哪些地方读取？
2. 为什么不直接用全局变量存 `session_dir` 和 `thread_id`？并发请求时会出什么问题？
3. `reset_session_context` 在哪里被调用？为什么必须在 `finally` 块里调用？

**🎯 核心点**
- 存 `session_dir`（工具读取工作目录路径）和 `thread_id`（monitor 定向推送 WebSocket 消息）
- 全局变量在并发时会被互相覆盖（请求 A 的 `session_dir` 被请求 B 覆盖），导致文件写到错误目录或消息推给错误客户端
- `reset_session_context` 在 `run_deep_agent` 的 `finally` 块里调用，无论成功还是异常都要清理，防止 ContextVar 泄漏到下一次请求（协程复用场景）

---

### L8 · 文件管理机制 ⏱10min

**📍 Preview**
项目有两个文件夹：`output/`（Agent 生成的文件）和 `updated/`（用户上传的文件），通过 `session_id` 做会话隔离。读 [api/server.py L55-L97](file:///Users/ys/code/ai_agent/deep_search_pro/api/server.py) + [agent/main_agent.py L51-L92](file:///Users/ys/code/ai_agent/deep_search_pro/agent/main_agent.py)。

**❓ Question 种子**
1. 用户上传的文件存在 `updated/session_{id}/`，Agent 生成的文件存在 `output/session_{id}/`——为什么要分两个目录，能不能合并成一个？
2. `run_deep_agent` 里把 `updated/` 的文件 `shutil.copy2` 到 `output/` 的逻辑，为什么要复制而不是直接让 Agent 读 `updated/` 里的文件？
3. `download` 接口里有安全检查（`is_relative_to(output_abs)`），防的是什么攻击？

**🎯 核心点**
- 分目录：语义清晰（输入 vs 输出）+ 权限隔离（可以对 output 设只读下载，对 updated 设只写上传）+ 清理策略不同（output 可定期归档，updated 用完即删）
- 复制到 output：让 Agent 只需要知道一个工作目录（`session_dir`），简化 Prompt 中的路径说明；同时把上传文件纳入归档
- 防路径遍历攻击（Path Traversal）：恶意请求 `path=../../etc/passwd` 如果不校验可以下载任意文件

---

### L9 · 迁移到 credit-ops 的设计 ⏱20min

**📍 Preview**
把 deep_search_pro 的多 Agent 编排经验落地到信贷贷后场景。无特定源码，开放设计题。

**❓ Question 种子（开放）**
1. credit-ops 的 Router Agent 该用 `create_deep_agent`（LLM 自主路由）还是 LangGraph 的条件路由（确定性路由）？权衡什么？
2. RAGFlow 的"先查助手列表再提问"两步走模式，在 credit-ops 里有什么优化空间？
3. `monitor.report_tool` 的埋点模式如果要迁移到 credit-ops，你会怎么设计它的 SSE 事件结构？

**🎯 核心点**（无标准答案，学员自由发挥）
- 信贷场景问题类型是可枚举的（SQL / KB / 复杂决策），建议用条件路由（确定性强、可解释、出错好排查）；create_deep_agent 适合问题类型模糊的开放场景
- 优化：把助手名 + 知识库描述做进系统 Prompt 的静态部分，避免每次都查一次；或者启动时缓存助手列表
- SSE 事件可以沿用 `event_type`（tool_start / assistant_call / task_result），配合 data-agent 的 `stream_mode="custom"` 推 JSON payload

**💡 这关结束后**：在 `docs/learning/deep-search-pro-architecture.md` 末尾追加「我的质疑与思考」小节（>= 3 条自己写的观点）

---

### Final · 合上文档 5 分钟口述 ⏱10min

**动作**：合上所有源码和文档，对着手机录音 5 分钟讲完以下内容：
1. deep_search_pro 整体架构（60s）
2. 一次请求的完整链路：HTTP → 后台任务 → WebSocket 推回（60s）
3. 三大面试考点各 60s：多 Agent 编排 / 工具调用 / WebSocket 监控
4. 与 data-agent 的核心差异 + 迁移到 credit-ops 的关键取舍（60s）

**通关标准**：录完自己听一遍，不卡壳、不看稿、术语准确。

---

## 🔗 §6 关联文档

| 文件 | 作用 |
|---|---|
| `../learning/deep-search-pro-architecture.md` | 知识地图（阶段 1-2 交付物，精读后产出）|
| [./learning-journey.md](./learning-journey.md) | data-agent 学习旅程卡（阶段 1-1）|
| [./plan.md](./plan.md) | 45 天总计划（双轨版）|
| [./progress.md](./progress.md) | 日志（每次收工追加一条）|
| [./README.md](./README.md) | 项目一页纸总览 |

---

## 📝 §7 学员使用提示

1. **每次开新会话**：把这个文件 @ 给 AI，说"继续学习 deep_search_pro"即可
2. **每次收工**：更新 §4 当前进度快照（30 秒）
3. **每过一关**：在 §5 「通关记录」勾选对应的 `- [ ]` → `- [x]`
4. **过完 3-4 关**：主动跟 AI 说"做 Mini Feynman"触发口述检查
5. **全部通关后**：在 [progress.md](./progress.md) 追加"阶段 1-2 彻底收官"日志，切换到 U3 能力抽象

> 💬 遇到 AI 不按规范执行（代读代码/直接给答案）→ 引用本文件 §3 禁止事项提醒。
