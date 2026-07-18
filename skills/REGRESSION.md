# Skill 触发与行为回归

用于验证三个同仓 skill 的 description 触发边界与交付行为。人工或 Agent 按表执行；发现漏检则回填对应文件。

## 1. 发现测试（description 匹配）

| # | 用户话术 | 期望加载 | 不应抢占 |
|---|----------|----------|----------|
| 1 | 「给 Project 加一个按 id 查询的 Controller」 | `coding-standard` | `code-simplification` |
| 2 | 「实现用户重置密码接口」 | `test-driven-development` + `coding-standard` | — |
| 3 | 「这段 Service 能跑但太绕，帮我简化」 | `code-simplification` | `test-driven-development` |
| 4 | 「按规范 review 这段 Vue 列表页」 | `coding-standard` | `test-driven-development` |
| 5 | 「修一下空指针：更新项目时 id 不存在」 | `test-driven-development` + `coding-standard` | — |
| 6 | 「用 TDD 写分页查询」 | `test-driven-development`（主）+ `coding-standard` | — |
| 7 | 「解释一下这个 Mapper 在干什么」 | 无强制 skill | 勿仅因「代码」误开编码流程 |
| 8 | 「先写实现，测试以后再补」 | `test-driven-development` 拦截合理化 | 禁止跳过 Red |
| 9 | 「组件里直接 fetch URL 拉列表」 | `coding-standard`（前端 API/错误） | — |
| 10 | 「declutter 这个工具函数」 | `code-simplification` | `coding-standard` 可并存但不改行为 |

**判定：** 对每条话术，检查 agent 是否 Read 了期望 skill；误触发记入下方「漏检回填」。

## 2. 行为测试（开 skill 后输出）

| 场景 | 期望行为 |
|------|----------|
| 新增 Java 写接口 | 声明按 coding-standard；Read `shared/*` + `java/backend.md`；业务在 Service；交付前自检 |
| 新增 Vue 列表 | API 不在组件内硬编码；有 loading/错误提示；无随意 `any` |
| 修 bug | 先失败测试再改实现（TDD）；不过早重构 |
| 简化已通过测试的代码 | 行为不变；小步修改；跑测试验证 |

## 3. 对抗测试

| 借口 / 红旗 | 期望 |
|-------------|------|
| 「小改动不用分层」 | 拒绝；仍落正确层 |
| 「catch 空着先过编译」 | 拒绝；Red Flag（见 `shared/universal.md`） |
| 「跳过自检直接交」 | 拒绝；必须 Read `shared/self-review.md` |
| 「先实现再补测试」 | TDD 铁律拦截 |

## 4. 本次回归结论与回填（2026-07-18）

| 发现 | 回填位置 |
|------|----------|
| 吞异常 / 空 catch 易被漏检 | `shared/self-review.md` Java：「无空 catch」；`shared/universal.md` Red Flags |
| 组件内硬编码 URL 缺少清单项 | `shared/self-review.md` 前端：「API 归属」；`typescript/frontend.md` 正反例 |
| 「另起一套风格」缺少显式项 | `shared/self-review.md` 通用：「风格对齐」；合理化借口表 |
| 「解释代码」易误开编码守门 | `SKILL.md`：「何时不激活」 |
| TDD description 缺 WHAT/关键词 | `test-driven-development` frontmatter 已重写 |
| coding-standard description WHAT 偏弱 | frontmatter 已补 naming/layering/exception/checklist |
| 关键已模块化 | 正反例/Red Flags 写入 `shared/universal.md`，长示例留在语言子文件 |

## 5. 复跑检查清单

- [ ] 跑完第 1 节 10 条话术，记录实际加载 skill
- [ ] 任选第 2 节 1 个 Java + 1 个 Vue 场景实作，确认自检声明
- [ ] 抽 2 条第 3 节对抗话术，确认被拒绝
- [ ] 新漏检项追加到本节表格并改对应 markdown
