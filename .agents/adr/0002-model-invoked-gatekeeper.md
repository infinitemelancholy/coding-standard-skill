# ADR-0002: coding-standard 设为 model-invoked 而非 user-invoked

**日期**: 2026-07-13
**状态**: 已采纳

## 背景

`coding-standard` 是所有代码生成/修改的强制性质量守门人。需要决定其调用模式——由 Agent 自动触发还是仅用户手动调用。

## 决策

设为 **model-invoked**（不设置 `disable-model-invocation`），Agent 在识别到代码生成/修改任务时自动激活。

## 理由

1. **守门人定位**：如果依靠用户手动输入 `/coding-standard` 来触发，存在遗漏风险。Agent 必须主动激活才符合"永远在线"的语义。
2. **可发现性**：`description` 中包含丰富的触发短语（「写代码、改 bug、加接口、加组件、重构、code review、实现功能、生成样板代码」），Agent 能根据任务类型自动匹配。
3. **与其他技能协同**：`test-driven-development` 和 `code-simplification` 也都是 model-invoked，三者由 Agent 自动组合激活，用户无需手动编排。

## 后果

- Agent 在每次代码生成/修改时都会消耗初始 token 来阅读本技能
- 需要通过 `SKILL.md` 开头的简短声明（「正在按 coding-standard 生成/修改代码」）让用户感知技能已激活
- 如果未来出现 token 预算紧张的场景，可考虑在特定项目配置中降级为 user-invoked
