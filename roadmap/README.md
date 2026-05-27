# 信贷 AI Agent 作品集 — 总纲

> 目标：将教学项目改造为金融信贷场景的求职作品集
> 核心展示：Agent 工程能力（架构设计、评估体系、可靠性）
> 状态：计划中，未执行

---

## 整体定位

以"信贷业务智能分析平台"为统一主题，四个子项目各负责一个能力层：

| 子项目 | 能力 | 基于 | 计划文件 |
|--------|------|------|---------|
| 问数 Agent | 自然语言查数据 | data-agent | [01-data-agent.md](./01-data-agent.md) |
| 知识库 Agent | 文档/规则问答 | knowledge_base | [02-knowledge-base.md](./02-knowledge-base.md) |
| 深度搜索 Agent | 多源信息聚合分析 | deep_search_pro | [03-deep-search.md](./03-deep-search.md) |
| 智能客服 Agent | 统一对话入口 | 待开发 | [04-customer-service.md](./04-customer-service.md) |

---

## 项目关系

```
用户提问
    ↓
智能客服（统一入口，意图路由）
    ├── 数据类问题  → 问数 Agent（NL2SQL）
    ├── 规则类问题  → 知识库 Agent（RAG）
    └── 综合分析类  → 深度搜索 Agent（多源聚合）
```

---

## 优先级（待确认）

> 待讨论确认

---

## vs 竞品的核心差异

- vs GitHub NL2SQL 项目：有数仓分层感知 + 语义层 + 评估体系
- vs Dify 工作流：代码级控制，可解释、可评估、可定制
- vs 通用金融 Agent：聚焦信贷经营分析，数据模型贴近真实

---

## 进度追踪

| 子项目 | 状态 |
|--------|------|
| 问数 Agent | 计划已定，未执行 |
| 知识库 Agent | 待讨论 |
| 深度搜索 Agent | 待讨论 |
| 智能客服 Agent | 待讨论 |
