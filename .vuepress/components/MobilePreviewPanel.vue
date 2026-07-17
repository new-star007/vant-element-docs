<template>
  <div v-if="showPanel" class="mp-panel">
    <div class="mp-phone">
      <div class="mp-notch"></div>
      <div class="mp-statusbar">
        <span class="mp-time">{{ time }}</span>
        <div class="mp-icons">
          <svg width="16" height="12" viewBox="0 0 18 12"><rect x="0.5" y="1.5" width="17" height="9" rx="1.5" fill="none" stroke="currentColor" stroke-width="1.2"/><rect x="3" y="4" width="12" height="4" rx="0.5" fill="currentColor"/></svg>
        </div>
      </div>
      <div class="mp-screen">
        <iframe
          v-if="demoUrl"
          :src="demoUrl"
          class="mp-iframe"
          frameborder="0"
          loading="lazy"
          allow="clipboard-read; clipboard-write"
        />
        <div v-else class="mp-placeholder">暂无演示</div>
      </div>
      <div class="mp-home">
        <div class="mp-home-bar"></div>
      </div>
    </div>
    <p class="mp-label">移动端预览</p>
  </div>
</template>

<script setup lang="ts">
import { computed, ref, onMounted, onUnmounted } from 'vue'
import { useRoute } from 'vuepress/client'

const DEMO_BASE = 'https://new-star007.github.io/vant-element-examples'

const EXAMPLE_ROUTES: Record<string, string> = {
  'list': 'list-demo',
  'list-page': 'list-page',
  'user-list': 'list',
  'user-table': 'user-table',
}

const route = useRoute()
const time = ref('09:41')
let timer: ReturnType<typeof setInterval> | undefined

function updateTime() {
  time.value = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
}

const showPanel = computed(() => {
  const path = route.path
  return /^\/(components|examples)\/[^/]+(\.html)?$/.test(path)
})

const demoUrl = computed(() => {
  const path = route.path
  const match = path.match(/^\/(?:components|examples)\/([^/.]+)/)
  if (!match) return ''
  const name = match[1]
  const routePath = EXAMPLE_ROUTES[name] || name
  return `${DEMO_BASE}/#/${routePath}`
})

onMounted(() => {
  updateTime()
  timer = setInterval(updateTime, 30000)
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})
</script>

<style>
@media (min-width: 1200px) {
  .vp-page {
    padding-right: 400px !important;
  }
}

.mp-panel {
  position: fixed;
  right: 20px;
  top: 80px;
  width: 375px;
  z-index: 100;
  pointer-events: auto;
}

.mp-phone {
  width: 100%;
  background: #1a1a1a;
  border-radius: 40px;
  padding: 12px;
  position: relative;
  box-shadow: 0 20px 60px rgba(0,0,0,0.3);
}

.mp-notch {
  position: absolute;
  top: 12px;
  left: 50%;
  transform: translateX(-50%);
  width: 150px;
  height: 28px;
  background: #1a1a1a;
  border-radius: 0 0 16px 16px;
  z-index: 1;
}

.mp-statusbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 20px 4px;
  color: #fff;
  font-size: 12px;
  font-weight: 600;
}

.mp-icons {
  display: flex;
  align-items: center;
  opacity: 0.8;
}

.mp-screen {
  background: #fff;
  border-radius: 24px;
  overflow: hidden;
  position: relative;
  z-index: 0;
  margin-top: -10px;
}

.mp-iframe {
  display: block;
  width: 100%;
  height: 667px;
  max-height: 80vh;
}

.mp-placeholder {
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 14px;
}

.mp-home {
  display: flex;
  justify-content: center;
  padding: 8px 0 4px;
}

.mp-home-bar {
  width: 120px;
  height: 4px;
  background: rgba(255,255,255,0.3);
  border-radius: 2px;
}

.mp-label {
  margin-top: 12px;
  font-size: 13px;
  color: #999;
  text-align: center;
}

@media (max-width: 1199px) {
  .mp-panel {
    display: none !important;
  }
}
</style>
