# Claude 开发导航
你是一个有10年业务经验的算法开发人员，代码能力一流，这是一个开发文档的导航。

## 1. 总原则

- 唯一主文档：`PRD_v3_final.md`
- 如果其他文件和 PRD 冲突：页面、路由、交互、数据口径都以 `PRD_v3_final.md` 为准
- 当前项目定位：可演示、可解释、可验证的 AI 客服 demo，不要包装成真实商用已上线系统

## 2. 开发前先读

按这个顺序看：

1. `PRD_v3_final.md`
2. `db/prisma/schema.prisma`
3. `db/schema.sql`
4. `scripts/seed-loader.ts`
5. `mock_ui/merged_chat_evidence_mock.html`
6. `mock_ui/handoff_workbench_mock.html`
7. `mock_ui/ops_console_abtest_mock.html`
8. `ai_cs_rag_v2_package/` 下的 mock 数据、RAG 数据、eval 数据

## 3. 做不同模块时看哪里

### 页面开发

- 看 `PRD_v3_final.md` 第 3-5 章
- 看 `mock_ui/` 下 3 个页面 mock
- 重点：`Inbox` 是会话+取证一体页，`Handoff` 是升级处理台，`Console` 是运营闭环后台

### 页面路由

- 看 `PRD_v3_final.md` 第 3 章和第 10.2 章
- 当前核心路由：
  - `/inbox`
  - `/handoff`
  - `/console`
  - `/console/experiments`
  - `/console/models`
  - `/console/prompts`
  - `/console/retrieval`
  - `/console/handoff-rules`
  - `/console/tone-templates`
  - `/console/badcases`
  - `/console/eval`
  - `/console/settings`
  - `/knowledge`
  - `/trace/:session_id`

### 模型路由

- 看 `PRD_v3_final.md` 第 5.5、6、12.3 章
- 必须是可配置路由，不要把模型写死
- 必须有 fallback model
- 路由要能挂到实验体系

### RAG / 检索

- 看 `PRD_v3_final.md` 第 1.2、5.1、7、5.11 章
- 看 `ai_cs_rag_v2_package/rag_source_docs_v2.jsonl`
- 看 `ai_cs_rag_v2_package/rag_schema_v3.json`
- 规则：RAG 是唯一真相源，业务事实回答必须可回溯 citation

### 会话流 / SSE

- 看 `PRD_v3_final.md` 第 5.1、10.3、12.1 章
- 事件流不只有 token，还包括：
  - `plan`
  - `thinking`
  - `reasoning`
  - `citation`
  - `done`
  - `error`
- `interrupt` 是正式能力，不要省略

### Handoff / Badcase

- 看 `PRD_v3_final.md` 第 5.2、5.8、9 章
- 所有非正常转人工默认进 badcase
- handoff 结构至少要有：原因、AI 摘要、证据状态、推荐动作、SLA

### A/B Test

- 看 `PRD_v3_final.md` 第 5.4、8 章
- 最少支持比较：
  - Prompt A/B
  - 检索策略 A/B
  - handoff 阈值
  - 情绪话术模板
  - 模型路由方案

### 数据库 / Prisma / Migration

- 看 `PRD_v3_final.md` 第 11、12 章
- 看 `db/prisma/schema.prisma`
- 看 `db/schema.sql`
- 看 `db/migrations/20260415_init/migration.sql`
- 如果三者冲突，先对齐 PRD，再回写 schema

### Seed / Mock 数据

- 看 `scripts/seed-loader.ts`
- 看 `scripts/generate_portfolio_assets.py`
- 看 `ai_cs_rag_v2_package/` 下所有 `csv/json/jsonl`
- seed 至少要覆盖：RAG、eval、订单、物流、退款、赔付、风控信号

### Eval / Console

- 看 `PRD_v3_final.md` 第 2.2、5.12、5.13、9 章
- 看 `ai_cs_rag_v2_package/eval_batch1_v2_expanded.csv`
- 看 `ai_cs_rag_v2_package/eval_multiturn_supplement_v1.csv`
- 看 `ai_cs_rag_v2_package/eval_rubric_guide.md`

## 4. 开发时必须遵守

- 不要把 prompt、retrieval、handoff rules、tone templates、model routes、experiments 写死
- 不要绕开 RAG 直接回答兼容性、保修、退换规则、物流承诺等业务事实
- 不要把这个 demo 做成普通客服后台；差异化重点是：
  - thinking / reasoning 可见
  - citation 可见
  - trace 可回放
  - badcase 可归因
  - A/B 可验证

## 5. 推荐实施顺序

1. 先搭路由骨架和 layout
2. 再做 `Inbox`、`Handoff`、`Console` 三个 P0 页面壳子
3. 再接 Prisma schema 和基础 API
4. 再接 seed-loader 和 mock 数据
5. 再补 SSE、citation、reasoning trace
6. 最后补 model routing、retrieval 配置、A/B Test、badcase、eval

## 6. 遇到不确定时

按下面顺序决策：

1. 看 `PRD_v3_final.md`
2. 看对应模块的 `mock_ui`
3. 看 `db/prisma/schema.prisma`
4. 看已有脚本和 mock 数据
5. 如果仍然冲突，保持和 `PRD_v3_final.md` 一致
