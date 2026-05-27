# 问数 Agent 改造计划（基于 data-agent）

> 状态：计划中，未执行

## 定位

基于数据中台的信贷经营分析 Agent。底层是符合企业规范的模拟信贷数据中台（DWD/DWS/ADS 分层），上层是具备分层路由和口径管理能力的 NL2SQL Agent。

## 核心亮点

1. **数仓分层感知**：Agent 知道该查 ADS 还是 DWD，不会用原始表直接算指标
2. **语义层管理**：贷余、逾期率等指标的计算口径写进元数据，Agent 不会算错
3. **歧义澄清**：遇到"查逾期率"这类不明确的问题，先澄清再查
4. **评估体系**：30 个测试用例，能量化说出准确率

## 业务背景

- 产品类型：个人消费贷，分期还款
- 产品设定（3 个）：消费分期 12 期 / 消费分期 6 期 / 小额消费贷 3 期
- 渠道设定（5 个）：自营 APP / 京东金融 / 抖音 / 线下门店-北京 / 线下门店-上海
- 数据量：6 个月历史数据，约 30000 笔放款记录

## 能力边界

本 Agent 只做一件事：把用户的自然语言转成 SQL，查库返回结果。

**回答条件（必须同时满足）：**
1. 问题可以用 SQL 表达
2. 答案存在于当前数据库的表中

**拒答示例：**
- “这个客户会不会逾期” → 不是查询，是预测
- “帮我建个模型” → 不是查询，是建模
- “该不该放款” → 不是查询，是决策
- “今天天气怎么样” → 数据库里没有

**拒答话术：**
> “抱歉，我只能回答数据查询类问题（如指标统计、趋势对比等）。您的问题超出了我的能力范围。”

---

## 数据库表结构

```
维度层（3张）
  dim_product                产品维度
  dim_channel                渠道维度
  dim_customer               客户维度

DWD 明细层（6张）
  dwd_account_detail         账户粒度宽表（当前状态快照：总额度、可用额度、账户状态）
  dwd_credit_apply_detail    授信粒度宽表（申请、审批）
  dwd_credit_limit_detail    额度粒度宽表（变动流水：初始授信/提额/降额/冻结/解冻）
  dwd_loan_detail            放款/借据粒度宽表
  dwd_repay_plan_detail      还款计划粒度宽表
  dwd_repay_record_detail    还款记录粒度宽表

DWS 轻度聚合层（4张）
  dws_customer_behavior      客户行为汇总（累计借款次数、在贷笔数、历史逾期次数）
  dws_loan_performance       借据还款表现（已还期数、剩余本金、是否首逾）
  dws_channel_product_summary 渠道×产品月度汇总（放款额、回款额、逾期率）
  dws_repay_ability          客户还款能力评估（近3期准时率、平均逾期天数）

  DWS 命中场景示例：
  - “累计借款超过5次的客户有多少？” → dws_customer_behavior
  - “当前有逾期的借据有多少笔？” → dws_loan_performance
  - “上个月各渠道各产品的回款率” → dws_channel_product_summary
  - “近3期都准时还款的客户占比” → dws_repay_ability

ADS 应用层（7张）
  ads_daily_business_summary 每日主要经营情况统计表
  ads_placement_daily        业务投放日表
  ads_placement_by_model     日投放报表按业务模式
  ads_channel_repay_report   各渠道应还/实还报表
  ads_customer_stats         客户情况统计表
  ads_business_count         业务笔数统计表
  ads_business_monitor       业务检测表（异常监控）

合计：20 张表
```

## 建设顺序

| 序号 | 任务 | 说明 | 预估时间 |
|------|------|------|--------|
| 1 | 建表 SQL | 新建 `docker/mysql/init/02_credit_schema.sql`，含全部 20 张表结构 | 1 小时 |
| 2 | 模拟数据生成脚本 | 新建 `scripts/generate_credit_data.py`，按真实分布生成 | 2 小时 |
| 3 | 改造 meta 元数据 | 清空电商示例，重新绘制信贷表/字段/指标元数据 + meta_config.yaml | 半天 |
| 4 | 30 个测试用例 | 覆盖经营/风险/渠道/趋势/歧义 5 类问题 | 2 小时 |
| 5 | Agent 逻辑改造 | 加入分层路由、口径澄清、拒答机制 | 1-2 天 |
| 6 | 跑评估迭代 | 记录准确率，调整元数据，迭代 1-2 轮 | 半天-1 天 |

## 文件改动范围

```
新增：
  data-agent/docker/mysql/init/02_credit_schema.sql
  data-agent/scripts/generate_credit_data.py

修改：
  data-agent/docker/mysql/init/01_create_databases.sql（清空电商示例数据）
  data-agent/conf/meta_config.yaml
  data-agent/app/agent/（路由和澄清逻辑）
```

---

## 待解决问题

1. **分层路由技术方案**：Agent 怎么知道该查哪一层？需要确定具体实现机制（Prompt / classifier / 规则匹配）
2. **口径澄清触发规则**：什么情况下 Agent 会追问？命中哪些指标时触发？追问后如何接续？
3. **多轮对话支持**：是否支持“再按产品拆一下”这类追问？待确认
