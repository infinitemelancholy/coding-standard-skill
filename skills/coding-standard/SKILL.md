---
name: coding-standard
description: >-
  通用编码质量守门人。在生成、修改、重构任何 Java 后端或 TypeScript/Vue 前端代码之前必须阅读并遵守；
  生成后必须对照自检清单逐项 Code Review，修正后再输出。
  触发场景：写代码、改 bug、加接口、加组件、重构、code review、实现功能、生成样板代码。
---

# 通用编码技能（Coding Standard）

你是编码质量守门人。凡涉及代码生成或修改，必须先完成本 Skill，再动手；写完后必须按第 7 部分自检，通过后才可交付。

**开始时简短声明：**「正在按 coding-standard 生成/修改代码。」

---

## 第1部分：技能定位与触发条件

### 何时激活

以下任一情况必须激活本 Skill：

- 用户要求编写、修改、重构、补全代码
- 新增 Controller / Service / Mapper / 组件 / API / Store
- 修复 bug、补异常处理、补注释
- 用户说「按规范写」「生成代码」「实现某某功能」

### 使用前提

1. **先读项目现有写法**：同层同类文件的命名、分层、注解、错误处理，优先对齐现有风格（R1）
2. **不绑定单一仓库**：规范通用；示例以 center（Java）与 Vue3+TS 为参考，落到具体项目时替换为该项目的包名/路径
3. **写完必自检**：未走完第 7 部分检查清单，不得宣称任务完成（R7）

---

## 第2部分：通用编码质量标准（语言无关）

目标风格：像有约 3 年全栈经验、吃透当前项目架构的中级工程师（R2）。

### 禁止模式

| 禁止 | 说明 |
|------|------|
| 冗余封装 | 一层只转发、无业务含义的 wrapper / 过度抽象 |
| 兜圈子实现 | 能 `Stream.filter` / `list.filter` 就不要多层 `for-if-continue` |
| 「AI 味」代码 | 生硬命名、不自然缩写、无意义中间变量、注释复述代码 |
| 复制粘贴膨胀 | 重复逻辑应抽私有方法或工具，而不是整段拷贝 |
| 过早优化 | 无证据的缓存、异步、复杂设计模式 |

### 要求模式

- 直截了当：每一步对应明确业务含义
- 方法单一职责；单方法建议不超过 **50 行**（组装复杂 DTO / 构建复杂 SQL 可例外，但需分段清晰）
- 私有方法按功能分组，用分隔注释：

```text
// ======================== 查询辅助方法 ========================
```

- 变量名自解释；能靠命名说清的，不写废话注释
- 优先复用项目已有工具类、基类、统一响应与异常体系

### 代码气味速查

- [ ] 是否有只调用一次且无复用价值的私有方法被过度拆分？
- [ ] 是否存在可合并的连续判空 / 连续转换？
- [ ] 是否用了项目里已有的工具却又手写了一套？
- [ ] 控制流是否能用早期返回（guard clause）变平？

---

## 第3部分：注释标注规范（★核心★）

对**外部类 / 外部方法 / 跨模块依赖**的调用处，在**行尾或紧邻上一行**做简要标注，便于后期查阅与 Debug（R3）。

### 行尾标注格式

```text
// [来源模块或包路径] 类名或符号名 - 作用简述，位置：相对路径或依赖坐标
```

### 标注三要素

1. **来源模块**：属于哪个模块/包（如 `center-common-data`、`hutool-core`、`@/api/project`）
2. **作用简述**：一句话说明干什么
3. **代码位置**：仓库相对路径，或第三方依赖的明确坐标

### 标注范围

**必须标注：**

- 跨模块业务类、公共组件、自定义注解
- 第三方工具类的关键调用（首次出现或非显而易见处）
- 项目内 API / Store / 工具的 import 或关键调用

**可省略：**

- 语言/标准库常识（如 `String`、`List`、`Optional`）
- 同一文件内私有方法
- 连续多行使用同一符号时，在首次出现处标注即可

### Java 后端标注示例

```java
// [center-common-data] DictResolver - 字典解析器，根据 type/value 反查标签，位置：center-common/center-common-data/.../resolver/DictResolver.java
String bizLevelName = DictResolver.getDictItemLabel("biz_level", dto.getBizLevel());

// [center-common-core] CheckedException - 业务检查异常，由全局异常处理转为统一错误响应，位置：center-common/center-common-core/.../exception/CheckedException.java
throw new CheckedException("项目ID不能为空");

// [center-common-security] @HasPermission - 权限校验注解，基于 Spring Security @PreAuthorize，位置：center-common/center-common-security/.../annotation/HasPermission.java
@HasPermission("project_projectMain_view")

// [hutool-core] StrUtil - 空安全字符串工具，位置：第三方依赖 cn.hutool.core.util.StrUtil
if (StrUtil.isBlank(dto.getProjectName())) { ... }

// [center-common-core] R - 统一响应包装，位置：center-common/center-common-core/.../util/R.java
return R.ok(result);
```

### TypeScript 前端标注示例

```typescript
// [@/api/project] getProjectList - 项目相关后端接口封装，位置：src/api/project.ts
import { getProjectList } from '@/api/project'

// [@/stores/project] useProjectStore - Pinia 项目状态，位置：src/stores/project.ts
const projectStore = useProjectStore()

// [@/utils/request] request - Axios 封装实例，位置：src/utils/request.ts
import request from '@/utils/request'
```

### 落地注意

- 路径按**当前仓库真实结构**填写；不确定时先搜索再写，禁止编造路径
- 第三方依赖写清 `groupId`/`artifact` 或 npm 包名即可
- 标注保持一行、信息密度高，不要写成大段文档注释

---

## 第4部分：Java 后端编码规范

参考风格：center 类 Spring Boot 多模块项目（Controller / Service / ServiceImpl / Mapper / Entity / DTO / VO）。具体类名以当前项目为准。

### 4.1 分层职责

| 层 | 职责 | 禁止 |
|----|------|------|
| Controller | 参数接收、权限/日志注解、调用 Service、包装 `R` | 塞业务逻辑、直接操作 Mapper |
| Service / ServiceImpl | 业务规则、事务、校验、编排 | 返回与协议强耦合的 Http 细节 |
| Mapper | 数据访问 | 业务判断 |
| Entity | 表映射 | 塞大量展示逻辑 |
| DTO / VO | 入参 / 出参 | 与 Entity 混用职责不清 |

### 4.2 推荐骨架（对齐 center 习惯）

**Controller**

- `@RestController` + `@RequiredArgsConstructor` + `@RequestMapping`
- 权限：`@HasPermission("...")`（若项目有）
- 操作日志：`@SysLog("...")`（若项目有）
- 返回：`R.ok(data)` / `R.failed(msg)`

**ServiceImpl**

- `@Service` + `@Slf4j` + `@RequiredArgsConstructor`
- 若用 MyBatis-Plus：可继承 `ServiceImpl<Mapper, Entity>`
- 多表写操作：`@Transactional(rollbackFor = Exception.class)`

**Entity**

- 继承项目 `BaseEntity<T>`（若有）
- `@TableName` + `@Data` + `@EqualsAndHashCode(callSuper = true)`

### 4.3 命名规范

| 类型 | 约定 | 示例 |
|------|------|------|
| Controller | `XxxController` | `ProjectController` |
| Service 接口 | `XxxService` | `ProjectService` |
| 实现类 | `XxxServiceImpl` | `ProjectServiceImpl` |
| Mapper | `XxxMapper` | `ProjectMapper` |
| Entity | 业务名，不加后缀或按项目惯例 | `Project` |
| DTO / VO / Query | `XxxDTO` / `XxxVO` / `XxxQuery` | `ProjectDTO` |
| 方法 | 动词开头，见名知意 | `getById` / `saveProject` / `pageQuery` |

命名必须**先对齐当前模块已有风格**，再套上表。

### 4.4 异常处理

- 业务可预期错误：`throw new CheckedException("明确中文/项目约定文案")`（或项目等价业务异常）
- Controller 若需显式捕获：`return R.failed(e.getMessage())`；优先依赖全局异常处理
- 不要吞异常；记录日志时带上下文（关键 id、操作名）
- 禁止 `catch (Exception e) {}` 空块；禁止用异常做常规控制流

### 4.5 事务管理

- 多表写入、先写后读一致性要求：`@Transactional(rollbackFor = Exception.class)`
- 事务方法放在 Spring 管理的 Public 方法上，避免同类自调用导致事务失效
- 只读查询一般不加写事务；需要时可 `@Transactional(readOnly = true)`（若项目使用）

### 4.6 常用框架标注速查

| 符号 | 来源 | 作用 |
|------|------|------|
| `ServiceImpl` / `Wrappers` | mybatis-plus | CRUD 与条件构造 |
| `StrUtil` / `CollUtil` / `ObjectUtil` | hutool-core | 空安全工具 |
| `@Data` / `@RequiredArgsConstructor` | lombok | 样板代码生成 |
| `@HasPermission` | 项目 security 模块 | 权限 |
| `R` | 项目 common-core | 统一响应 |
| `CheckedException` | 项目 common-core | 业务异常 |

### 4.7 参考实现片段

```java
@Slf4j
@RestController
@RequiredArgsConstructor
@RequestMapping("/project")
public class ProjectController {

    private final ProjectService projectService;

    // [center-common-security] @HasPermission - 接口权限，位置：.../annotation/HasPermission.java
    @HasPermission("project_projectMain_view")
    @GetMapping("/{id}")
    public R<ProjectVO> get(@PathVariable Long id) {
        return R.ok(projectService.getDetail(id));
    }
}
```

```java
@Service
@Slf4j
@RequiredArgsConstructor
public class ProjectServiceImpl extends ServiceImpl<ProjectMapper, Project> implements ProjectService {

    @Override
    @Transactional(rollbackFor = Exception.class)
    public void updateProject(ProjectDTO dto) {
        if (dto.getId() == null) {
            // [center-common-core] CheckedException - 业务校验失败，位置：.../exception/CheckedException.java
            throw new CheckedException("项目ID不能为空");
        }
        Project existing = getById(dto.getId());
        if (existing == null) {
            throw new CheckedException("项目不存在");
        }
        // ... 更新逻辑
        log.info("更新项目成功, id={}", dto.getId());
    }
}
```

---

## 第5部分：TypeScript / Vue 前端编码规范

适用于 Vue 3 + TypeScript；React 等框架时迁移原则不变：类型安全、错误兜底、职责单一、标注外部依赖。

### 5.1 组件结构

- 优先 `<script setup lang="ts">`
- 模板 → script → style 顺序清晰；复杂逻辑抽到 `composables/`
- 一个组件一件事；页面组件负责编排，展示组件负责 UI

### 5.2 API 与错误处理

- 接口集中在 `api/`（或项目约定目录），组件内不散落 URL
- 每个调用使用 try-catch（或统一封装的错误拦截），给用户可读提示
- 加载态 / 空态 / 错误态要有着落

```typescript
async function loadList() {
  loading.value = true
  try {
    // [@/api/project] getProjectList - 项目列表接口，位置：src/api/project.ts
    const res = await getProjectList(query)
    list.value = res.data ?? []
  } catch (e) {
    console.error(e)
    // 使用项目既有的 message / notification 组件提示
  } finally {
    loading.value = false
  }
}
```

### 5.3 状态管理

- 跨页面共享状态用 Pinia（或项目既有方案）
- 局部 UI 状态留在组件内（`ref` / `reactive`）
- 禁止直接修改 props；用 `emit` 或回调上抛

### 5.4 类型定义

- 类型集中在 `types/`（或与模块同目录的 `types.ts`）
- 禁止随意 `any`；确需时写注释说明原因
- Props / Emits / API 响应都要有明确类型

---

## 第6部分：异常兜底检查清单（R4）

生成或修改代码时，逐项考虑：

| 检查项 | 说明 |
|--------|------|
| 参数非空校验 | Controller/DTO：`@NotNull` / `@NotBlank` 等；Service：关键参数再防一层 |
| 存在性校验 | update / delete 前先查，记录不存在则业务异常 |
| 唯一性校验 | 新增/修改查重；修改时用 `ne` 排除自身 |
| 事务回滚 | 多表写：`@Transactional(rollbackFor = Exception.class)` |
| 日志记录 | 关键路径 `log.info`；失败 `log.warn` / `log.error`，带业务键 |
| 资源释放 | IO / 流 / 连接用 try-with-resources 或等价 finally |
| 输入校验 | 前端表单校验 + 后端再校验，不信任前端 |
| 空值处理 | 集合/可选值避免 NPE；前端可选链与默认值 |
| 并发安全 | 共享可变状态有策略（版本号、锁、DB 约束等，按场景） |
| 权限与安全 | 接口鉴权；防 SQL 拼接注入；防 XSS；密钥不进仓库 |

---

## 第7部分：Code Review 自检流程（★必须执行★）

### 触发时机

**代码生成或修改完成后、向用户交付之前**，必须执行本流程。不得跳过。

### 执行步骤

1. 重读本 Skill 中与当前语言相关的章节与清单
2. 对照下方清单逐项审查刚写的代码
3. 发现问题立即修正
4. 修正后再快速扫一遍
5. 全部通过后才输出最终结果；可在回复中用极简列表说明已自检（无需长篇报告）

### Java 后端自检清单

| 检查项 | 通过标准 |
|--------|----------|
| 分层正确 | Controller 只接参与包装响应；业务在 Service；数据访问在 Mapper |
| 异常完整 | 可能为 null 的返回有处理；写操作有事务或明确错误路径 |
| 注释标注 | 外部类/方法调用处有「来源 + 作用 + 位置」标注 |
| 命名规范 | 与项目现有风格一致 |
| 事务边界 | 多表写有 `@Transactional(rollbackFor = Exception.class)` |
| 日志覆盖 | 关键步骤有 info；异常有 error/warn |
| 无冗余 | 无废变量、多余转换、可扁平化的嵌套 |

### TypeScript 前端自检清单

| 检查项 | 通过标准 |
|--------|----------|
| 类型安全 | 无无必要 `any`；Props/Emits/API 类型完整 |
| API 错误处理 | 有 try-catch 或统一拦截，并有用户提示 |
| 职责单一 | 复杂逻辑在 composable；组件不臃肿 |
| 注释标注 | 关键 import / 外部调用有标注 |
| 响应式正确 | `ref`/`reactive` 使用正确；不直接改 props |
| UI 边界 | 加载 / 空 / 错误 / 边界数据有处理 |

### 通用自检清单

| 检查项 | 通过标准 |
|--------|----------|
| 无硬编码 | 魔法值进常量/枚举/配置 |
| 可读性 | 命名即可表达意图 |
| 可测试性 | 职责单一、依赖可替换 |
| 安全性 | 无注入/XSS；敏感信息未硬编码 |
| 性能 | 无 N+1；无无必要嵌套循环；大列表有分页或虚拟化 |
| 可扩展 | 预留扩展点合理，不做过度设计（R5） |

---

## 第8部分：扩展机制（R5 / R6）

本 Skill 按章节扩展，新增规则时：

| 规则类型 | 添加到 |
|----------|--------|
| 语言无关的质量/气味规则 | 第2部分 |
| 注释标注规则 | 第3部分 |
| Java / Spring / MyBatis 等 | 第4部分 |
| TS / Vue / 前端工程化 | 第5部分 |
| 异常与安全兜底 | 第6部分 |
| 自检项 | 第7部分 |

### 添加新规则的写法

1. 用一句话写清**必须做什么 / 禁止做什么**
2. 给一个**最小示例**（最好带标注格式）
3. 若需 Agent 每次检查，同步在第 7 部分加一行清单
4. 保持通用：写「模式」，项目专有类名放在示例里并注明「以当前仓库为准」

### 跨平台使用

| 平台 | 用法 |
|------|------|
| TRAE | `.trae/skills/coding-standard/SKILL.md`（本文件） |
| Cursor | 复制到个人/项目 Skill，或纳入规则；编码前 `@coding-standard` |
| Claude Code | 将正文纳入 `CLAUDE.md` 或项目 Skill |
| Copilot | 可将精简版写入 `.github/copilot-instructions.md` |

内容为纯 Markdown，迁移时保留 YAML frontmatter 或按目标平台要求微调即可。

---

## 快速执行清单（每次编码任务）

1. [ ] 阅读本 Skill（至少第2、3、6、7 部分 + 对应语言章节）
2. [ ] 查看项目同层现有代码，对齐风格
3. [ ] 实现功能（直截了当，无冗余）
4. [ ] 外部依赖补齐行尾标注
5. [ ] 按第7部分自检并修正
6. [ ] 交付最终代码
