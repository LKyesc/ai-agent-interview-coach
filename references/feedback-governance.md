# 真实反馈治理

STATE_DIR 的选择和初始化遵循 [setup.md](setup.md)。原始反馈追加至 feedback.md，假设/正式规则/事实确认追加至 calibration.md，规则变化追加至 changelog.md。源 Skill 和安装 Skill 不各建状态。

## 收集与拟议修改

原始记录保留日期、公司/部门、岗位、轮次、形式、结果、原问题、候选人回答、面试官原话、可观察行为、候选人感受、附件来源。未知填“未提供”；原文与模型解释分栏，不能反向生成面试官原话或由拒绝结果推断原因。

主 Agent 为每项拟议修改给出 item_id、current_rule、proposed_rule、applicable_role、supporting_evidence、opposing_evidence、expected_benefit、overfit_risk。审查前只登记 pending_review，不改正式规则。

## 必须单独派发的反馈审查

使用 `collaboration.spawn_agent`，`fork_turns: "none"`，task_name 为唯一 feedback_review ID。message 只含候选人事实摘录及来源、原始反馈/附件摘录、当前规则、拟议修改、下列审查和输出合同，不传主 Agent 内部推理、期望结论或逐答评分作为已定结论。审查员只读，不能直接修改状态。这是独立于 answer-review 的实际调用；每答审查不能充当规则审查。

要求逐项核实：用户感受是否被写成面试官事实、单场泛化、事实冲突、虚构指标/贡献/上线风险、规则是否改善追问、影响范围及可回滚性。

输出逐项固定字段：`item_id, evidence_class, confidence, contradiction, overfit_risk, verdict, rationale, allowed_update`，附来源/短原文证据和 rollback_action。

evidence_class 取 `明确反馈/可观察信号/候选人解释/结果相关/信息不足`；混合输入拆项。verdict 取 `接受/作为假设接受/拒绝/需用户澄清`。allowed_update 明确允许的最小修改和层级；拒绝或澄清不允许正式更新。

审查不可用：只在 feedback.md 保留原文、pending_review 和原因，告知尚未校准；不以主 Agent 自审替代。报告真实返回后记录代理 ID 和全文。

## 提升与回滚

所有正式变化都必须通过独立反馈审查；用户明确确认长期规则也不免除此门。审查接受后，至少有一个条件满足才进入正式层：明确面试官反馈且审查认为证据与岗位相符；两个独立面试重复出现同类信号；用户明确确认长期采用此规则。相同面试的多份回忆不算独立证据。其余可接受结论存为待验证假设，并记录支持/反对证据、岗位、置信度和验证方式。

每次变化追加日期、item_id、旧值、新值、证据、审查 ID/结论、适用范围和精确回滚动作。回滚也追加记录，恢复指定旧版本，保留历史。候选人解释如“感觉他不喜欢 RAG”不能单场删除正式 RAG 题路；可形成表达/深挖待验证假设。

## 事实确认

反馈不能自动改候选人事实。用户明确确认后，在 calibration.md 的事实确认区保存原话、时间、provenance、`user-reported/unverified` 或 `artifact-verified`、材料摘录、冲突、处理状态和适用范围。验证等级不是用户可信度评价，只表示材料核验状态。与现有事实冲突时同时保留双方来源，先核实，不静默覆盖 candidate-profile。拟改事实源文件须有用户明确确认、对照来源及变更记录。

