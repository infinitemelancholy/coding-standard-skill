# Java 后端编码规范

参考风格：Spring Boot 多模块项目（Controller / Service / ServiceImpl / Mapper / Entity / DTO / VO）。具体类名以当前项目为准。

## 分层职责

| 层 | 职责 | 禁止 |
|----|------|------|
| Controller | 参数接收、权限/日志注解、调用 Service、包装 `R` | 塞业务逻辑、直接操作 Mapper |
| Service / ServiceImpl | 业务规则、事务、校验、编排 | 返回与协议强耦合的 Http 细节 |
| Mapper | 数据访问 | 业务判断 |
| Entity | 表映射 | 塞大量展示逻辑 |
| DTO / VO | 入参 / 出参 | 与 Entity 混用职责不清 |

## 推荐骨架

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

## 命名规范

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

## 异常处理

- 业务可预期错误：`throw new CheckedException("明确中文/项目约定文案")`（或项目等价业务异常）
- Controller 若需显式捕获：`return R.failed(e.getMessage())`；优先依赖全局异常处理
- 不要吞异常；记录日志时带上下文（关键 id、操作名）
- 禁止 `catch (Exception e) {}` 空块；禁止用异常做常规控制流

## 事务管理

- 多表写入、先写后读一致性要求：`@Transactional(rollbackFor = Exception.class)`
- 事务方法放在 Spring 管理的 Public 方法上，避免同类自调用导致事务失效
- 只读查询一般不加写事务；需要时可 `@Transactional(readOnly = true)`（若项目使用）

## 常用框架速查

| 符号 | 来源 | 作用 |
|------|------|------|
| `ServiceImpl` / `Wrappers` | mybatis-plus | CRUD 与条件构造 |
| `StrUtil` / `CollUtil` / `ObjectUtil` | hutool-core | 空安全工具 |
| `@Data` / `@RequiredArgsConstructor` | lombok | 样板代码生成 |
| `@HasPermission` | 项目 security 模块 | 权限 |
| `R` | 项目 common-core | 统一响应 |
| `CheckedException` | 项目 common-core | 业务异常 |

## 参考实现片段

```java
@Slf4j
@RestController
@RequiredArgsConstructor
@RequestMapping("/project")
public class ProjectController {

    private final ProjectService projectService;

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

## 反面教材与修正对照

> 每组对照以 `// ❌` 标记反面、`// ✅` 标记修正，行为不变，仅提升可读性。

### 深层嵌套 → Guard Clause 提前返回

```java
// ❌ 深层嵌套：正常路径被埋在 else 深处
public void processOrder(Order order) {
    if (order != null) {
        if (order.getStatus() == OrderStatus.PENDING) {
            if (order.getAmount().compareTo(BigDecimal.ZERO) > 0) {
                doProcess(order);
            } else {
                throw new CheckedException("订单金额无效");
            }
        } else {
            throw new CheckedException("订单状态不允许操作");
        }
    } else {
        throw new CheckedException("订单不能为空");
    }
}

// ✅ Guard clause：异常路径先抛出，正常路径在最外层
public void processOrder(Order order) {
    if (order == null) {
        throw new CheckedException("订单不能为空");
    }
    if (order.getStatus() != OrderStatus.PENDING) {
        throw new CheckedException("订单状态不允许操作");
    }
    if (order.getAmount().compareTo(BigDecimal.ZERO) <= 0) {
        throw new CheckedException("订单金额无效");
    }
    doProcess(order);
}
```

### 手工集合构建 → Stream API

```java
// ❌ 手工 for-if-add：啰嗦，意图不直接
List<ProjectVO> activeProjects = new ArrayList<>();
for (Project project : projectList) {
    if (project.getStatus() == ProjectStatus.ACTIVE) {
        ProjectVO vo = new ProjectVO();
        vo.setId(project.getId());
        vo.setName(project.getName());
        activeProjects.add(vo);
    }
}

// ✅ Stream + map：意图一目了然
List<ProjectVO> activeProjects = projectList.stream()
        .filter(p -> p.getStatus() == ProjectStatus.ACTIVE)
        .map(p -> {
            ProjectVO vo = new ProjectVO();
            vo.setId(p.getId());
            vo.setName(p.getName());
            return vo;
        })
        .collect(Collectors.toList());
```

### 冗长条件赋值 → 三目 / Optional

```java
// ❌ if-else 赋值：三行干一行的事
String displayName;
if (user.getNickname() != null) {
    displayName = user.getNickname();
} else {
    displayName = user.getUsername();
}

// ✅ 三目：一行表达
String displayName = user.getNickname() != null ? user.getNickname() : user.getUsername();

// ✅ 或 Optional（适合链式判空场景）
String displayName = Optional.ofNullable(user.getNickname()).orElse(user.getUsername());
```

### 散落的重复校验 → 抽取方法

```java
// ❌ 校验逻辑在 add 和 update 两处重复粘贴
public void addProject(ProjectDTO dto) {
    if (StrUtil.isBlank(dto.getName())) {
        throw new CheckedException("项目名称不能为空");
    }
    if (dto.getName().length() > 100) {
        throw new CheckedException("项目名称不能超过100字");
    }
    // ... 新增逻辑
}

public void updateProject(ProjectDTO dto) {
    if (StrUtil.isBlank(dto.getName())) {
        throw new CheckedException("项目名称不能为空");
    }
    if (dto.getName().length() > 100) {
        throw new CheckedException("项目名称不能超过100字");
    }
    // ... 更新逻辑
}

// ✅ 抽取私有校验方法，单一来源
public void addProject(ProjectDTO dto) {
    validateProjectName(dto.getName());
    // ... 新增逻辑
}

public void updateProject(ProjectDTO dto) {
    validateProjectName(dto.getName());
    // ... 更新逻辑
}

private void validateProjectName(String name) {
    if (StrUtil.isBlank(name)) {
        throw new CheckedException("项目名称不能为空");
    }
    if (name.length() > 100) {
        throw new CheckedException("项目名称不能超过100字");
    }
}
```

### 冗余布尔逻辑 → 直接返回表达式

```java
// ❌ 多余的 if-true-else-false
public boolean isProjectActive(Project project) {
    if (project.getStatus() == ProjectStatus.ACTIVE && !project.getDeleted()) {
        return true;
    }
    return false;
}

// ✅ 直接返回布尔表达式
public boolean isProjectActive(Project project) {
    return project.getStatus() == ProjectStatus.ACTIVE && !project.getDeleted();
}
```
