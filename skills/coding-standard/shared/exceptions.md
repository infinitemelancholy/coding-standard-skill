# 异常兜底检查清单（R4）

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
