# Profile：行尾来源标注（line-annotations）

仅在标注 Profile 开启时由 `SKILL.md` 加载并执行。默认关闭。

对**外部类 / 外部方法 / 跨模块依赖**的调用处，在**行尾或紧邻上一行**做简要标注，便于后期查阅与 Debug（R3）。

## 行尾标注格式

```text
// [来源模块或包路径] 类名或符号名 - 作用简述，位置：相对路径或依赖坐标
```

## 标注三要素

1. **来源模块**：属于哪个模块/包（如 `center-common-data`、`hutool-core`、`@/api/project`）
2. **作用简述**：一句话说明干什么
3. **代码位置**：仓库相对路径，或第三方依赖的明确坐标

## 标注范围

**必须标注：**

- 跨模块业务类、公共组件、自定义注解
- 第三方工具类的关键调用（首次出现或非显而易见处）
- 项目内 API / Store / 工具的 import 或关键调用

**可省略：**

- 语言/标准库常识（如 `String`、`List`、`Optional`）
- 同一文件内私有方法
- 连续多行使用同一符号时，在首次出现处标注即可

## Java 后端标注示例

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

## TypeScript 前端标注示例

```typescript
// [@/api/project] getProjectList - 项目相关后端接口封装，位置：src/api/project.ts
import { getProjectList } from '@/api/project'

// [@/stores/project] useProjectStore - Pinia 项目状态，位置：src/stores/project.ts
const projectStore = useProjectStore()

// [@/utils/request] request - Axios 封装实例，位置：src/utils/request.ts
import request from '@/utils/request'
```

## 落地注意

- 路径按**当前仓库真实结构**填写；不确定时先搜索再写，禁止编造路径
- 第三方依赖写清 `groupId`/`artifact` 或 npm 包名即可
- 标注保持一行、信息密度高，不要写成大段文档注释
