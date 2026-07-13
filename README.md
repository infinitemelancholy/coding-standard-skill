# coding-standard-skill

通用编码 Skill 集合：质量规范 + 测试驱动 + 代码简化，供 AI Agent 在 Cursor / Claude Code 等环境使用。

适用于支持 [Agent Skills](https://agentskills.io/) 的工具。

## 本仓库包含的 Skills

| Skill | 用途 | 来源 |
|-------|------|------|
| `coding-standard` | 编码规范、行尾标注、异常兜底、生成后自检 | 本仓库原创 |
| `test-driven-development` | 先失败测试再写实现（Red-Green-Refactor） | [obra/superpowers](https://github.com/obra/superpowers)（MIT） |
| `code-simplification` | 行为不变下简化结构、提升可读性 | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) |

分工：新功能/修 bug 走 TDD；能跑但难读时走简化；交付质量始终遵守 `coding-standard`。

## 安装


```bash
npx skills add infinitemelancholy/coding-standard
```

## 使用

- Agent 会按各 Skill 的 `description` 自动选用；也可显式 `@coding-standard` / `@test-driven-development` / `@code-simplification`
- **更新：** `npx skills update`

## 仓库结构

```
skills/
├── coding-standard/
│   └── SKILL.md
├── test-driven-development/
│   ├── SKILL.md
│   ├── testing-anti-patterns.md
│   └── LICENSE-UPSTREAM
└── code-simplification/
    └── SKILL.md
```

第三方出处与许可证见 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)。

## License

- 本仓库原创内容（`coding-standard`、本 README 等）：MIT
- 上游 vendored Skills：见各自上游许可证与 `THIRD_PARTY_NOTICES.md`
