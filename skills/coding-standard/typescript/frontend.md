# TypeScript / Vue 前端编码规范

适用于 Vue 3 + TypeScript；React 等框架时迁移原则不变：类型安全、错误兜底、职责单一。

## 组件结构

- 优先 `<script setup lang="ts">`
- 模板 → script → style 顺序清晰；复杂逻辑抽到 `composables/`
- 一个组件一件事；页面组件负责编排，展示组件负责 UI

## API 与错误处理

- 接口集中在 `api/`（或项目约定目录），组件内不散落 URL
- 每个调用使用 try-catch（或统一封装的错误拦截），给用户可读提示
- 加载态 / 空态 / 错误态要有着落

```typescript
async function loadList() {
  loading.value = true
  try {
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

**Bad — 组件内散落 URL、滥用 any、直接改 props**

```typescript
async function load(props: any) {
  const res = await fetch('/api/project/list?id=' + props.id)
  props.list = await res.json() // 直接改 props
}
```

**Good — API 集中、类型明确、状态本地或上抛**

```typescript
async function loadList(projectId: number) {
  loading.value = true
  try {
    const res = await getProjectList({ projectId })
    list.value = res.data ?? []
  } catch (e) {
    console.error(e)
    // message.error(...)
  } finally {
    loading.value = false
  }
}
```

## 状态管理

- 跨页面共享状态用 Pinia（或项目既有方案）
- 局部 UI 状态留在组件内（`ref` / `reactive`）
- 禁止直接修改 props；用 `emit` 或回调上抛

## 类型定义

- 类型集中在 `types/`（或与模块同目录的 `types.ts`）
- 禁止随意 `any`；确需时写注释说明原因
- Props / Emits / API 响应都要有明确类型

## 反面教材与修正对照

> 每组对照以 `// ❌` 标记反面、`// ✅` 标记修正，行为不变，仅提升可读性。

### 深层嵌套 → Guard Clause 提前返回

```typescript
// ❌ 正常路径被埋在 else 深处
function processOrder(order: Order | null): void {
  if (order !== null) {
    if (order.status === 'pending') {
      if (order.amount > 0) {
        doProcess(order)
      } else {
        throw new Error('订单金额无效')
      }
    } else {
      throw new Error('订单状态不允许操作')
    }
  } else {
    throw new Error('订单不能为空')
  }
}

// ✅ Guard clause：异常先抛，正常路径平铺
function processOrder(order: Order | null): void {
  if (!order) throw new Error('订单不能为空')
  if (order.status !== 'pending') throw new Error('订单状态不允许操作')
  if (order.amount <= 0) throw new Error('订单金额无效')
  doProcess(order)
}
```

### 手工 for 循环 → filter/map/reduce

```typescript
// ❌ 手工构建数组
const activeUsers: UserVO[] = []
for (const user of users) {
  if (user.status === 'active') {
    activeUsers.push({ id: user.id, name: user.name })
  }
}

// ✅ filter + map 链式表达
const activeUsers = users
  .filter((u) => u.status === 'active')
  .map((u) => ({ id: u.id, name: u.name }))
```

### 冗长条件赋值 → 逻辑或 / 可选链 / 三目

```typescript
// ❌ if-else 赋值
let displayName: string
if (user.nickname) {
  displayName = user.nickname
} else {
  displayName = user.fullName
}

// ✅ 逻辑或（falsy 值可接受时）
const displayName = user.nickname || user.fullName

// ✅ 空值合并（仅 null/undefined 时启用，更精确）
const displayName = user.nickname ?? user.fullName

// ✅ 三目（需要显式布尔判断时）
const label = user.isAdmin ? '管理员' : '普通用户'
```

### 冗余 async/await → 直接返回 Promise

```typescript
// ❌ 多余 async/await：没有额外逻辑就别包一层
async function fetchUser(id: string): Promise<User> {
  return await userService.findById(id)
}

// ✅ 直接返回 Promise
function fetchUser(id: string): Promise<User> {
  return userService.findById(id)
}
```

### 冗余布尔返回 → 直接返回表达式

```typescript
// ❌ if-true-else-false
function isValid(input: string): boolean {
  if (input.length > 0 && input.length <= 100) {
    return true
  }
  return false
}

// ✅ 直接返回表达式
function isValid(input: string): boolean {
  return input.length > 0 && input.length <= 100
}
```

### 组件臃肿 → 提取 Composable

```typescript
// ❌ 所有逻辑堆在组件内
<script setup lang="ts">
const searchKeyword = ref('')
const list = ref<Project[]>([])
const loading = ref(false)
const page = ref(1)
const total = ref(0)

async function fetchList() { /* 20行 */ }
function handleSearch() { /* ... */ }
function handlePageChange(p: number) { /* ... */ }
function formatDate(d: string) { /* ... */ }
function getStatusLabel(s: number) { /* ... */ }
</script>

// ✅ 数据逻辑抽到 composable，组件只负责编排
// composables/useProjectList.ts
export function useProjectList() {
  const list = ref<Project[]>([])
  const loading = ref(false)
  const page = ref(1)
  const total = ref(0)

  async function fetchList() { /* ... */ }
  return { list, loading, page, total, fetchList }
}

// 组件内
<script setup lang="ts">
const { list, loading, page, total, fetchList } = useProjectList()
</script>
```
