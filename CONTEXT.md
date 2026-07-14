# Coding Standard Skills

本仓库的 Agent 技能集合，供 AI Agent 在生成代码时使用。

## 语言

**编码守门人（Coding Gatekeeper）**：
`coding-standard` 技能的角色——任何代码生成/修改都必须先读且后检的强制性质量规范。
Agent 在产出代码前必须声明「正在按 coding-standard 生成/修改代码」，完成后走完第7部分自检清单。

**可选 Profile（Opt-in Profile）**：
`coding-standard` 内部的模块化规则组。默认关闭（不加载对应文件）以节省 token。
由项目层 `.coding-standard.yaml`（`annotations: true`）或用户口头指令激活。
当前只有 `line-annotations` 一个 Profile。

**自检 / Code Review**：
`coding-standard` 第7部分定义的代码生成后自检流程。Agent 生成代码后必须逐项对照清单检查并修正，全部通过后才可交付。

**行尾来源标注（Line Annotations）**：
可选 Profile `line-annotations` 的内容。在代码行尾标注外部依赖的「来源 + 作用 + 位置」。
格式与示例见 `skills/coding-standard/profiles/line-annotations.md`。

**RED / GREEN / REFACTOR**：
`test-driven-development` 技能的核心循环——先写失败测试、验证测试失败、写最小实现让测试通过、重构。
不是所有测试一次写完：一次只做一个 RED→GREEN→REFACTOR 循环。

**行为不变（Preserve Behavior）**：
`code-simplification` 技能的第一原则——简化代码结构但不改变任何输入输出、边界条件或副作用。

## 关系

- `coding-standard` 是永远在线的守门人，始终配合同仓其他技能
- `test-driven-development` 在新功能/修 bug/改行为时激活，与 `coding-standard` 同时生效
- `code-simplification` 在代码可读性差时激活，与 `coding-standard` 同时生效
- `THIRD_PARTY_NOTICES.md` 记录 vendored 技能的出处与许可证

## 已消除的歧义

- 「标注」不再指"注释标注"，明确指「行尾来源标注 Profile」（可选）
- 「检查」不再泛指，明确指「第7部分自检流程」（强制性）
- 「开关」与「Profile」等价——都指条件加载的可选规则模块
