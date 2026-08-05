# InfiniteScroll 无限滚动

InfiniteScroll 是一个指令，用于实现列表滚动到底部附近时自动加载更多数据，适合长列表的分页加载场景。

## 基础用法

通过 `v-infinite-scroll` 指令绑定加载回调，当滚动容器的底部距离小于 100px 时自动触发。结合加载状态与结束状态即可实现完整的无限滚动列表：

```vue
<template>
  <div class="scroll-container" v-infinite-scroll="loadMore">
    <div v-for="item in list" :key="item" class="scroll-item">
      <el-card shadow="hover" style="margin-bottom: 8px;">列表项 {{ item }}</el-card>
    </div>
    <div v-if="loading" style="text-align: center; padding: 16px;">
      <el-spinner :size="24" />
    </div>
    <div v-if="finished" style="text-align: center; padding: 16px; color: #909399; font-size: 14px;">
      没有更多了
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: [],
      loading: false,
      finished: false,
      count: 0
    }
  },
  mounted() {
    this.loadMore()
  },
  methods: {
    loadMore() {
      if (this.loading || this.finished) return
      this.loading = true
      setTimeout(() => {
        for (let i = 0; i < 10; i++) {
          this.list.push(this.list.length + 1)
        }
        this.loading = false
        this.count++
        if (this.count >= 5) {
          this.finished = true
        }
      }, 1000)
    }
  }
}
</script>

<style scoped>
.scroll-container {
  height: 400px;
  overflow-y: auto;
  border: 1px solid #eee;
  border-radius: 4px;
  padding: 8px;
}
</style>
```

## 何时使用

- **长列表分页**：当列表数据量较大，需要滚动加载而不是分页按钮时。
- **无限信息流**：社交动态、消息列表等持续追加数据的场景。
- **减少首屏压力**：按需加载数据，避免一次性渲染大量 DOM。

## 注意事项

- 指令绑定在**滚动容器**上，容器需要设置固定高度并开启 `overflow-y: auto` 才会产生滚动。
- 加载回调应自行处理**加载中**与**加载结束**状态，避免重复触发。
- 建议容器底部预留加载提示（如 Spinner），提升用户体验。

## API

### Directive Attributes

| Name | Description | Type | Default |
| ---- | ----------- | ---- | ------- |
| v-infinite-scroll | 滚动到底部附近（距底部小于 100px）时触发的加载回调 | Function | — |
