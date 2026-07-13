# coding-standard

通用编码 Skill：让 AI Agent 在生成 Java 后端 / TypeScript（Vue）前端代码前先读规范，写完后对照清单自检。

适用于 Cursor、TRAE、Claude Code 等支持 [Agent Skills](https://agentskills.io/) 的工具。

## 安装

使用官方 Skills CLI（通过 npm 的 `npx` 运行）：

```bash
npx skills add infinitemelancholy/coding-standard
```

只安装本 Skill，并指定 Cursor + TRAE（全局）：

```bash
npx skills add infinitemelancholy/coding-standard --skill coding-standard -a cursor -a trae -g -y
```

### 常用选项

| 命令 | 说明 |
|------|------|
| `-g` | 安装到用户目录（所有项目可用） |
| `-a cursor` / `-a trae` | 只装到指定 Agent |
| `--skill coding-standard` | 只安装本仓库中的该 Skill |
| `-y` | 跳过确认提示 |

## 使用

安装后无需额外配置。Agent 会根据 Skill 的 `description` 在编码任务中自动选用。

- **Cursor**：也可在对话中 `@coding-standard`
- **TRAE**：装到 `.trae/skills/coding-standard` 后由平台发现
- **更新**：`npx skills update coding-standard`

## 仓库结构

```
skills/
└── coding-standard/
    └── SKILL.md
```

## License

MIT
