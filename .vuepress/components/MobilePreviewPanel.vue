<template>
  <div v-if="showPanel" class="mp-overlay">
    <div class="mp-device">
      <div class="mp-device-inner">
        <div class="mp-earpiece"></div>
        <div class="mp-statusbar">
          <span class="mp-time">{{ time }}</span>
          <div class="mp-status-icons">
            <svg width="16" height="12" viewBox="0 0 18 12"><rect x="0.5" y="1.5" width="17" height="9" rx="1.5" fill="none" stroke="currentColor" stroke-width="1.2"/><rect x="3" y="4" width="12" height="4" rx="0.5" fill="currentColor"/></svg>
          </div>
        </div>
        <div class="mp-screen">
          <iframe
            ref="iframeRef"
            :src="currentUrl"
            class="mp-iframe"
            frameborder="0"
            loading="lazy"
            allow="clipboard-read; clipboard-write"
          />
          <div v-if="!currentUrl" class="mp-placeholder">暂无演示</div>
        </div>
        <div class="mp-home">
          <div class="mp-home-indicator"></div>
        </div>
      </div>
    </div>
    <p class="mp-label">移动端预览</p>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'
import { useRoute, useRouter } from 'vuepress/client'

const DEMO_BASE = import.meta.env.PROD
  ? 'https://new-star007.github.io/vant-element-examples'
  : 'http://localhost:8080'

const ROUTE_MAP: Record<string, string> = {
  'list': 'list-demo',
  'list-page': 'list-page',
  'user-list': 'list',
  'user-table': 'user-table',
}

const REVERSE_ROUTE_MAP: Record<string, string> = {
  'list-demo': '/components/list-demo.html',
  'list-page': '/components/list-page.html',
  'list': '/examples/user-list.html',
  'user-table': '/examples/user-table.html',
}

function showPanelFor(path: string): boolean {
  return /^\/(components|examples)(\/|$)/.test(path)
}

function getDemoRouteName(path: string): string {
  const match = path.match(/^\/(?:components|examples)\/([^/.]+)/)
  if (!match) return 'home'
  return ROUTE_MAP[match[1]] || match[1]
}

function getDocPath(demoPath: string): string {
  if (!demoPath) return ''
  if (demoPath === '/' || demoPath === '/home') return '/components/'
  return REVERSE_ROUTE_MAP[demoPath.slice(1)] || `/components${demoPath}.html`
}

const route = useRoute()
const router = useRouter()
const showPanel = computed(() => showPanelFor(route.path))
const iframeRef = ref<HTMLIFrameElement>()
const time = ref('09:41')
const currentUrl = ref(DEMO_BASE + '/#/' + getDemoRouteName(route.path))
let timer: ReturnType<typeof setInterval> | undefined

function updateTime() {
  time.value = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
}

function updatePagePadding(show: boolean) {
  if (import.meta.env.SSR) return
  document.body.classList.toggle('mp-has-preview', show)
}

function navigateIframe(name: string) {
  if (!name) return
  const iframe = iframeRef.value
  if (iframe?.contentWindow?.postMessage) {
    try { iframe.contentWindow.postMessage({ type: 'navigate', path: '/' + name }, '*') } catch { /* ignore */ }
  }
}

function navigateTo(path: string) {
  updatePagePadding(showPanel.value)
  const name = getDemoRouteName(path)
  if (!name) return
  currentUrl.value = `${DEMO_BASE}/#/${name}`
  navigateIframe(name)
}

function handleIframeMessage(ev: MessageEvent) {
  if (ev.data?.type !== 'navigate') return
  const docPath = getDocPath(ev.data.path)
  if (!docPath || docPath === route.path) return
  router.push(docPath).catch(function () {})
}

watch(() => route.path, navigateTo)

onMounted(() => {
  updateTime()
  timer = setInterval(updateTime, 30000)
  window.addEventListener('message', handleIframeMessage)
  navigateTo(route.path)
})

onUnmounted(() => {
  updatePagePadding(false)
  window.removeEventListener('message', handleIframeMessage)
  if (timer) clearInterval(timer)
})
</script>

<style>
body.mp-has-preview .vp-page {
  padding-right: 420px !important;
}
@media (max-width: 1344px) {
  body.mp-has-preview .vp-page {
    padding-right: 380px !important;
  }
}

.mp-overlay {
  position: fixed;
  right: 24px;
  top: 80px;
  width: 375px;
  z-index: 100;
}

.mp-device {
  background: linear-gradient(145deg, #1a1a1a, #222);
  border-radius: 44px;
  padding: 14px;
  box-shadow:
    0 0 0 1px rgba(255,255,255,0.06),
    0 30px 60px rgba(0,0,0,0.35),
    0 0 80px rgba(0,0,0,0.1);
  position: relative;
}

.mp-device::before {
  content: '';
  position: absolute;
  left: -4px;
  top: 22%;
  width: 4px;
  height: 36px;
  background: #333;
  border-radius: 2px;
}

.mp-device::after {
  content: '';
  position: absolute;
  right: -4px;
  top: 26%;
  width: 4px;
  height: 48px;
  background: #333;
  border-radius: 2px;
}

.mp-device-inner {
  background: #fff;
  border-radius: 32px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  position: relative;
}

.mp-earpiece {
  position: absolute;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 120px;
  height: 26px;
  background: #1a1a1a;
  border-radius: 0 0 14px 14px;
  z-index: 10;
}

.mp-earpiece::after {
  content: '';
  position: absolute;
  top: 8px;
  left: 50%;
  transform: translateX(-50%);
  width: 40px;
  height: 4px;
  background: #333;
  border-radius: 2px;
}

.mp-statusbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 22px 4px;
  color: #333;
  font-size: 12px;
  font-weight: 600;
}

.mp-status-icons {
  display: flex;
  align-items: center;
  opacity: 0.6;
}

.mp-screen {
  position: relative;
  min-height: 220px;
}

.mp-iframe {
  display: block;
  width: 100%;
  height: 667px;
  max-height: 75vh;
}

.mp-placeholder {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 14px;
}

.mp-home {
  display: flex;
  justify-content: center;
  padding: 8px 0 10px;
}

.mp-home-indicator {
  width: 120px;
  height: 4px;
  background: #ddd;
  border-radius: 2px;
}

.mp-label {
  margin-top: 12px;
  font-size: 13px;
  color: #999;
  text-align: center;
}

@media (max-width: 1199px) {
  .mp-overlay {
    display: none !important;
  }
  body.mp-has-preview .vp-page {
    padding-right: 0 !important;
  }
}
</style>
