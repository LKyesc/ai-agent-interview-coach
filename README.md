# AI Agent Interview Coach

可持续迭代的 AI Agent 技术／AI 应用开发模拟面试 Skill。每轮只问一题，独立 Subagent 评分并指出有证据的不足；支持补充事实复审和真实面试反馈校准。

## 使用

把本目录放入运行环境的个人 skills 目录，目录名保持 ai-agent-interview-coach。调用：

```text
$ai-agent-interview-coach 开始 Agent 技术岗位一面
$ai-agent-interview-coach 这是我的面试反馈，请复盘并校准
```

首次提供自己的简历或介绍一个项目。默认教练模式、技术一面、35 分钟；考试模式可将分数留到结束。

独立审查需要环境支持真实 Subagent 调用；本包使用 Codex collaboration.spawn_agent 调用合同。缺少工具时明确标记未审查，不生成伪造独立评分。评分是训练参考，不是任何公司的官方标准或录用概率。

## 文件

SKILL.md 为入口；agents/ 为界面配置；references/ 含追问、评分、事实字段和反馈治理；templates/ 为完整资料和记录模板。所有 Skill 文件引用都在包内。

真实资料默认存于运行项目的 .interview-coach/，已由 .gitignore 排除。包内不包含原始个人简历、真实训练记录、公司定向面经链接或固定本机路径。初始题路示例须根据使用者真实项目适配。

用户补充事实时保留来源和验证等级；原回答表达分保持，实际重答后独立评分。规则迭代经过单独审查，保留依据与回滚记录。

此包由原个人 Skill 通用化导出；原个人版本及本地训练记录不在发布包内。开源许可证尚未指定，请发布者自行选择。

