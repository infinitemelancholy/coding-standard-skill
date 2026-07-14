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

## 状态管理

- 跨页面共享状态用 Pinia（或项目既有方案）
- 局部 UI 状态留在组件内（`ref` / `reactive`）
- 禁止直接修改 props；用 `emit` 或回调上抛

## 类型定义

- 类型集中在 `types/`（或与模块同目录的 `types.ts`）
- 禁止随意 `any`；确需时写注释说明原因
- Props / Emits / API 响应都要有明确类型
