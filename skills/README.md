# Skills

本仓库的技能集合。三个技能可同时生效：

| Skill | 作用 | 触发时机 | 来源 |
|-------|------|----------|------|
| `coding-standard` | 编码守门人——质量规范 + 自检清单 | 任何代码生成/修改 | 原创 |
| `test-driven-development` | Red-Green-Refactor 循环 | 新功能 / 修 bug / 改行为 | obra/superpowers (MIT) |
| `code-simplification` | 行为不变的结构简化 | 能跑但难读 / 过复杂 | addyosmani/agent-skills |

## 协同规则

- `coding-standard` 是**永远在线的守门人**——Agent 在生成或修改任何 Java / TypeScript / Vue 代码时必须激活
- `test-driven-development` 在**新功能、修 bug、改行为**时与 `coding-standard` 同时激活
- `code-simplification` 在**代码能跑但难读**时与 `coding-standard` 同时激活
- 可同时生效：TDD / 简化管流程与结构，coding-standard 管交付规范
