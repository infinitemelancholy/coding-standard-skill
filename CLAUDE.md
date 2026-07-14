# Coding Standard Skills

本仓库的技能集合：

| Skill | 职责 | 来源 |
|---|---|---|
| `coding-standard` | 编码质量守门人——任何代码生成/修改都必须先读且后检的强制性规范 | 原创 |
| `test-driven-development` | Red-Green-Refactor 循环——写功能/修 bug/改行为时的标准流程 | obra/superpowers（MIT） |
| `code-simplification` | 行为不变的代码结构简化——能跑但难读时用 | addyosmani/agent-skills |

## 技能协同规则

- **coding-standard** 是永远在线的守门人（model-invoked）。Agent 在生成或修改任何 Java 后端 / TypeScript/Vue 前端代码时，必须激活此技能。代码生成完毕后，必须完成第7部分自检清单才可交付。
- **test-driven-development** 在新功能、修 bug、改行为时激活。Agent 必须先写失败测试、再写最小实现、再重构。
- **code-simplification** 在代码能跑但难读/过复杂时激活。Agent 不改变行为，只简化结构和命名。
- 三个技能可同时生效：TDD/简化管流程与结构，coding-standard 管交付规范。

## Profile 机制（coding-standard）

`coding-standard` 内含可选 Profile——行尾来源标注。**默认关闭**以节省 token。

开启条件（激活 `coding-standard` 时检查）：
1. 项目根目录存在 `.coding-standard.yaml` 且 `annotations: true`
2. 或用户口头要求（如「按 coding-standard 开启标注」）
3. 满足任一条件 → Read 加载 `profiles/line-annotations.md` 并执行其规则
4. 否则 → 不加载，不生成行尾标注，自检时跳过标注相关项

## 仓库结构

```
skills/
├── README.md                 # 技能索引 + 协同规则
├── coding-standard/
│   ├── SKILL.md              # 主入口（加载规则 + Profile 机制 + 快速清单）
│   ├── shared/
│   │   ├── universal.md      # 通用编码质量标准（语言无关）
│   │   ├── exceptions.md     # 异常兜底检查清单
│   │   └── self-review.md    # Code Review 自检流程（Java + TS + 通用清单）
│   ├── java/
│   │   └── backend.md        # Java 后端编码规范
│   ├── typescript/
│   │   └── frontend.md       # TypeScript/Vue 前端编码规范
│   └── profiles/
│       └── line-annotations.md  # 可选：开启后才加载
├── test-driven-development/
│   ├── SKILL.md              # Red-Green-Refactor 流程
│   ├── testing-anti-patterns.md
│   └── LICENSE-UPSTREAM
└── code-simplification/
    └── SKILL.md              # 行为不变的简化五原则
```

## 维护规则

- 添加、删除、重命名或改变任何技能的行为后：
  - 更新顶层 `README.md` 的技能表
  - 同步 `skills/README.md` 的索引
  - 更新 `coding-standard/SKILL.md` 中的「同仓协作 Skills」表
  - 若涉及第三方来源，同步 `THIRD_PARTY_NOTICES.md`
- `coding-standard` 规则变更时：
  - 通用质量规则 → `shared/universal.md`
  - Java 规范 → `java/backend.md`
  - TS/Vue 规范 → `typescript/frontend.md`
  - 异常兜底 → `shared/exceptions.md`
  - 自检项 → `shared/self-review.md`
  - 可选 Profile → `profiles/` + SKILL.md 顶部 Profile 表
- Profile 规则变更时：更新 `coding-standard/SKILL.md` 顶部的 Profile 表和加载规则
- 新增 Skill 时，遵循先写 SKILL.md → 更新 README → 更新 skills/README.md → 检查协同表 → 提交的顺序
