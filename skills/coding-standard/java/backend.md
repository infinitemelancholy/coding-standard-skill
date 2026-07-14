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
