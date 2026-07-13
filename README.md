# coding-standard-skill

通用编码 Skill 集合：质量规范 + 测试驱动 + 代码简化，供 AI Agent 在 Cursor / Claude Code 等环境使用。

适用于支持 [Agent Skills](https://agentskills.io/) 的工具。

## 本仓库包含的 Skills

| Skill | 用途 | 来源 |
|-------|------|------|
| `coding-standard` | 编码规范、异常兜底、生成后自检；行尾标注为可选 Profile | 本仓库原创 |
| `test-driven-development` | 先失败测试再写实现（Red-Green-Refactor） | [obra/superpowers](https://github.com/obra/superpowers)（MIT） |
| `code-simplification` | 行为不变下简化结构、提升可读性 | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) |

分工：新功能/修 bug 走 TDD；能跑但难读时走简化；交付质量始终遵守 `coding-standard`。

### 可选 Profile：行尾来源标注

`coding-standard` 的行尾来源标注**默认关闭**（省输入/输出 token）。需要时可：

1. **项目配置**：在项目根目录创建 `.coding-standard.yaml`：

```yaml
annotations: true
```

2. **口头开启**：对 Agent 说「按 coding-standard 开启标注」

开启后，Agent 会按需 Read [`profiles/line-annotations.md`](skills/coding-standard/profiles/line-annotations.md) 并生成/自检行尾标注。本仓库根目录不放该配置文件，避免开发 Skill 时误开。

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
│   ├── SKILL.md
│   └── profiles/
│       └── line-annotations.md   # 可选；默认不加载
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
