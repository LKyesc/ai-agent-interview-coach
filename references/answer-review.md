# 每答独立审查合同

每个候选人回答必须实际派发一个独立 Subagent，使用 `collaboration.spawn_agent`，`fork_turns: "none"`。每次新答案/补充复评使用新鲜上下文；不要继承主 Agent 历史、内部推理、预期分数或结论。报告返回前不发布独立评分。仅提示“请独立评分”或自己扮演审查员不满足合同。工具不可用时记录 `unreviewed` 和原因，无伪造评分。

## 实际调用

读取本文件 rubric，然后构建以下 message 内容（字段内容取真实记录，不将字段名当作数据）。调用示例：

```text
collaboration.spawn_agent({
  task_name: "answer_review_<session>_<answer>_<revision>",
  fork_turns: "none",
  message: <包含以下输入字段、完整评分规则和输出合同的审查任务>
})
```

审查 Agent 只读、不得改文件或联系他人；工具返回的代理 ID 必须保存在审查记录，等待实际报告后再继续评分。输入完整包含：

- review_id、session_id、answer_id、revision、review_kind（original / supplement_reassessment / rewritten_answer）。
- role、round、mode、当前题要求和相关 rubric（下方八维及尺度）。
- exact_question、verbatim_answer； relevant_prior_answers 按原问题/原答逐条给出，仅选当前判断相关的内容。
- candidate_fact_excerpts：事实原文、来源位置、适用范围/禁止推断；不要只给主 Agent 归纳的结论。
- supplementary_facts：用户原文、日期/来源、`user-reported/unverified` 或 `artifact-verified`。artifact-verified 要给可核对材料摘录及路径；用户口头确认不等于材料验证。
- 已知冲突的双方原文和来源。补充复评附原答、原报告、补充事实；原报告用于保留变化历史，不是要求沿用结论。

要求审查员根据确切问题和原答独立判定，每个候选人答案都审查；前答只作相关背景，不能把前答或用户补充冒充本次原答。没有简历证据不是虚假证据；省略实现细节不是没做过实现。只有明确不相容的事实才判事实冲突，未知项标记待核实。

## 八维 rubric

1. 事实具体度：相关场景、输入输出和约束是否具体。
2. 因果与问题诊断：判断和定位链条是否有支撑。
3. 技术正确性：机制、状态和概念解释是否正确。
4. 方案比较与取舍：选择理由和替代方案是否回应问题。
5. 指标和证据可信度：口径、基线、来源及限制是否可信。
6. 个人贡献清晰度：本人动作、职责和团队边界是否清楚。
7. 工程落地与边界意识：失败处理、使用范围及限制是否清楚。
8. 反思、收获和迁移能力：复盘或迁移是否有依据。

各维 1–5 分：1=相关要求严重缺失/错误；2=部分回应但关键支撑缺失；3=基本回应且仍有具体缺口；4=充分具体、逻辑成立，仅有小缺口；5=充分且有可追溯证据/清楚取舍。不相关维度为 N/A 并说明理由，不能因当前题未涉及的内容扣分。先列本题实际要求，再选适用维度；扣分必须对应回应本题所必要的缺口。主动补充的陈述可以检查准确性，但不因此增加本题未要求的义务，例如回答架构选择理由时提到评测限制，不能据此扣“没有跨场景迁移”；讲角色分工也不自动要求提供代码行作为个人贡献证明。此类延展需求放入可选追问，不能作为当前答卷缺陷。`total = round(100 * sum(applicable_scores) / (5 * applicable_count), 1)`；全部 N/A 则总分 N/A。此分数是训练量表，不代表目标公司录用概率或官方标准。

## 必需输出

输出结构化报告：

- identifiers、review_kind、status（reviewed）。
- dimension_scores：八个维度各 score（1–5 或 N/A）、reason、quoted_answer_evidence；遗漏可标记“原答无相关表述”，不得编引文。
- applicable_count、normalized_total_100、confidence（low/medium/high，并说明证据限制）。
- prioritized_issues：通常 1–3 项；每项 severity、具体问题、原答短引文/明确遗漏、gap_class、reason、facts_needed。无真实缺陷可为空并解释。
- gap_class 仅使用 `expression omission`（原答表达遗漏）、`unavailable evidence`（证据未提供）、`technical error`、`fact contradiction`、`design limitation`。不得用前三种缺口直接推断没实施。
- factual_adequacy：已支持、尚未核实与冲突；supplement_reassessment 必须含 before、after、change_reason、仍未解决项。
- original_answer_score：原答分和保持/纠正原因；若发现原评分误读可明确纠错留痕，不能因为后续事实提高原答表达分。
- ONE proposed_follow_up：只一个焦点的问题；不要列题单。

## 补充与实际重答

用户可直接给审查员补充事实：主 Agent 原样转交 fresh-context supplement_reassessment 任务，不能先自行筛除不利证据。区分“原答案表达是否充分”和“补充后项目事实是否充分”；后者可改善，前者保持原分。只有用户实际给出新的答案才以 rewritten_answer 对新原文独立打分。保留原答、原审查、补充、复评、重答和变化理由；不覆盖前分。

将每次报告追加到 STATE_DIR 中的 `answer-reviews.md`。用户明确确认新事实时，在 STATE_DIR 中的 `calibration.md` 记录 provenance、验证等级、冲突和适用范围；原始 candidate-profile 不静默改写。有冲突时暂不将冲突事实作为已验证基线，逐项核实。

