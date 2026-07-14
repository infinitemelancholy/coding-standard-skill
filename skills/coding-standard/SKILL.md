---
name: coding-standard
description: >-
  通用编码质量守门人。在生成、修改、重构任何 Java 后端或 TypeScript/Vue 前端代码之前必须阅读并遵守；
  生成后必须对照自检清单逐项 Code Review，修正后再输出。
  触发场景：写代码、改 bug、加接口、加组件、重构、code review、实现功能、生成样板代码。
---

# 通用编码技能（Coding Standard）

你是编码质量守门人。凡涉及代码生成或修改，必须先完成本 Skill，再动手；写完后必须按自检清单自检，通过后才可交付。

**开始时简短声明：**「正在按 coding-standard 生成/修改代码。」

## Overview

在不绑定单一仓库的前提下，为 Java 后端与 TypeScript/Vue 前端代码提供统一的质量底线：先对齐项目现有风格，再按语言加载对应规范，最后通过强制自检清单收口交付质量。

它不负责把需求拆成测试循环，也不负责纯粹的结构简化；它负责兜住命名、分层、异常处理、边界条件和最终交付质量。需要改行为时配合同仓 `test-driven-development`，代码已正确但难读时配合同仓 `code-simplification`。

## 何时激活

以下任一情况必须激活本 Skill：

- 用户要求编写、修改、重构、补全代码
- 新增 Controller / Service / Mapper / 组件 / API / Store
- 修复 bug、补异常处理、补注释
- 用户说「按规范写」「生成代码」「实现某某功能」

## 加载规则

激活时按以下顺序 Read 子文件：

1. **必读** `shared/universal.md` —— 通用编码质量标准（禁止模式、要求模式、代码气味）
2. **按语言选读**：
   - Java 后端 → `java/backend.md`
   - TypeScript / Vue 前端 → `typescript/frontend.md`
   - 两者都涉及 → 都读
3. **异常门槛** `shared/exceptions.md` —— 生成/修改代码前扫一遍兜底点
4. **代码完成后必须 Read** `shared/self-review.md` 并逐项自检，通过后才可交付

## 可选 Profile（按需加载）

可选能力默认关闭；仅当开关打开时，用 Read 加载对应文件。

| Profile | 文件 | 默认 | 开启条件 |
|---------|------|------|----------|
| `line-annotations` | `profiles/line-annotations.md` | 关闭 | 见下方 |

**加载规则：**
1. 检测项目根目录是否存在 `.coding-standard.yaml`，且 `annotations: true`
2. 或用户口头要求开启标注（如「按 coding-standard 开启标注」）
3. 满足任一条件 → Read 加载 `profiles/line-annotations.md` 并执行其规则
4. 否则 → **不加载**，**不生成**行尾来源标注；自检跳过标注相关项

```yaml
# 项目根目录 .coding-standard.yaml
annotations: true
```

## 同仓协作 Skills

| 场景 | 使用 |
|------|------|
| 新功能、修 bug、改行为 | 先遵循同仓 `test-driven-development`（Red → Green → Refactor） |
| 行为已正确但难读/过复杂 | 遵循同仓 `code-simplification`（不改行为，只简化表达） |
| 命名、分层、异常、自检 | 始终遵循本 Skill |
| 行尾来源标注 | 仅当标注 Profile 已开启时遵循 |

三者可同时生效：TDD / 简化管流程与结构，本 Skill 管交付规范。

## 扩展机制

新增规则时：

| 规则类型 | 添加到 |
|----------|--------|
| 语言无关的质量/气味规则 | `shared/universal.md` |
| Java / Spring / MyBatis 等 | `java/backend.md` |
| TS / Vue / 前端工程化 | `typescript/frontend.md` |
| 异常与安全兜底 | `shared/exceptions.md` |
| 自检项 | `shared/self-review.md` |
| 行尾来源标注（及其他可选能力） | `profiles/` + 上方 Profile 表 |

添加新 Profile 时：在 `profiles/` 新增文件 → 在上方 Profile 表登记 → 默认关闭（省 token）。

## 跨平台使用

| 平台 | 用法 |
|------|------|
| TRAE | `.trae/skills/coding-standard/SKILL.md`（本文件） |
| Cursor | 复制到个人/项目 Skill，或纳入规则；编码前 `@coding-standard` |
| Claude Code | 将正文纳入 `CLAUDE.md` 或项目 Skill |
| Copilot | 可将精简版写入 `.github/copilot-instructions.md` |

## 快速执行清单

1. [ ] 声明「正在按 coding-standard 生成/修改代码」
2. [ ] Read `shared/universal.md`
3. [ ] 按语言 Read `java/backend.md` 或 `typescript/frontend.md`
4. [ ] 扫一眼 `shared/exceptions.md` 兜底点
5. [ ] 按 Profile 规则：需要时 Read `profiles/line-annotations.md`，否则跳过
6. [ ] 查看项目同层现有代码，对齐风格
7. [ ] 若属新功能/修 bug/改行为：按 `test-driven-development` 走 Red → Green → Refactor
8. [ ] 实现功能（直截了当，无冗余）；需要理清结构时用 `code-simplification`
9. [ ] 【若标注 Profile 已开启】外部依赖补齐行尾标注
10. [ ] Read `shared/self-review.md` 并按清单逐项自检
11. [ ] 交付最终代码
