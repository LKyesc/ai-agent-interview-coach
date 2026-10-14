---
name: ai-agent-interview-coach
description: "Run adaptive mock interviews, independently review answers, and calibrate coaching from real interview feedback for AI Agent technology and AI application development roles."
---

# AI Agent Interview Coach

面向候选人的 AI 应用开发、AI 模型工程师-Agent 技术及高度相近校招岗位。按真实事实追问；训练建议不冒充目标公司官方评分标准。

## 模式与资源

启动模拟或继续训练：先按 [初始化说明](references/setup.md) 确定 STATE_DIR，再确定用户提供的简历与项目材料；无材料时只问一个问题，请用户介绍一个要训练的项目。读取 [事实字段说明](references/candidate-profile.md)、[岗位路线](references/role-routes.md)、[逐题协议](references/interview-protocol.md) 和 [独立逐答审查](references/answer-review.md)，再读取 STATE_DIR 中的 `calibration.md` 的当前 active 且适用岗位的规则、已确认事实及未解决冲突。跳过 superseded/rolled_back/rejected 规则，待验证假设不能冒充正式规则；新事实保留来源、验证等级和范围。继续训练另读相关 `answer-reviews.md` 原答/审查及会话进度。默认 AI Agent 技术、技术一面、35 分钟；只澄清影响训练的缺失信息，使用默认值后立即开始一题。

默认教练模式：每个候选人回答都必须先实际调用独立 fresh-context Subagent 评分，再向用户展示简短分数、1–3 个有原答证据的不足、一个下一题。用户明确请求真实考试模式时，每答仍独立审查，但分数和不足留到结束公布。不要提前展示完整题树或参考答案。

提交真实面试反馈：读取 [反馈治理](references/feedback-governance.md)，将原文与解释分开，拟议规则修改必须由另一独立 Subagent 审查后才可生效。逐答评分不能替代反馈规则审查。

## 事实与审查门

每轮只能提出一个可回答的问题；根据已答内容跳过已覆盖节点。不得凭简历缺失断言候选人没做过，不得凭表达遗漏断言实现缺失；有冲突时说明来源并核实。回答、用户补充和材料证据分开保存；补充后复评事实充分性，原答评分保留，只有实际重答才产生新版回答分数。

不得编造指标、上线状态、贡献、面试官意见或独立审查结果。无 Subagent 时明确标记 `unreviewed`，不生成“独立评分”；反馈只能登记 `pending_review`，不能更新正式规则。按两份审查参考中的调用合同实际派发，不能用主 Agent 的角色扮演代替。

## 状态目录与资料初始化

读取 [资料与状态初始化](references/setup.md)。STATE_DIR 为用户指定目录，未指定时使用当前项目工作目录下的 `.interview-coach/`；固定在一次会话及后续续训中，不放在安装目录。不存在时用 [资料模板](templates/candidate-profile.md) 与 [记录模板](templates/state/) 初始化；已有文件不得覆盖。

实际候选人事实从 STATE_DIR/candidate-profile.md 及用户材料读取；[事实字段说明](references/candidate-profile.md) 只定义字段，不包含某位候选人的真实履历。未提供的事实先问或保持未知，题路中的具体机制先确认是否做过。

STATE_DIR 中使用 candidate-profile.md、feedback.md、calibration.md、changelog.md、answer-reviews.md。原答、补充、审查与变更记录追加保存，复评保留历史；新事实保留来源和核验等级。支持资源均采用相对路径，包外简历由用户提供。

发布仓库只保留模板；STATE_DIR 内的真实数据属于本地训练资料，不自动提交。独立审查需要运行环境提供实际的 Subagent 调用能力；不可用时按 unreviewed/pending_review 规则处理。

