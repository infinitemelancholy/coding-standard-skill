# ADR-0003: Vendoring 第三方技能而非自行重写

**日期**: 2026-07-13
**状态**: 已采纳

## 背景

`test-driven-development` 和 `code-simplification` 两个技能在开源社区已有高质量实现（`obra/superpowers` 的 TDD 技能、`addyosmani/agent-skills` 的 code-simplifier）。需要决定是自行重写还是直接引入。

## 决策

**直接 vendoring（引入）**第三方技能，而非自行重写。

## 理由

1. **质量保障**：`obra/superpowers` 的 TDD 技能经过了长期打磨和社区验证，其 Red-Green-Refactor 流程、常见合理化话术和反模式清单比自己从头写更完备。
2. **避免重复造轮子**：`code-simplification` 的"五原则 + 分步流程"已覆盖了代码简化的核心场景，自行重写不会显著更好。
3. **差异化聚焦**：本仓库的核心价值在于 `coding-standard`（语言无关的编码规范 + 异常兜底 + 自检清单 + Profile 机制），这是社区中没有现成替代品的部分。
4. **许可兼容**：两个上游技能均为 MIT 许可，与本仓库兼容。出处与许可证记录在 `THIRD_PARTY_NOTICES.md` 中。

## 后果

- `test-driven-development` 为英文，与中文 `coding-standard` 语言不一致，但不影响 Agent 理解
- 上游更新时需手动对比合并，但上游相对稳定
- vendored 技能中的「与同仓 Skill 的协同」声明是本仓库新增内容，上游没有
