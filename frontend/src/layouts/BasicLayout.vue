<template>
  <div class="basic-layout">
    <!-- 侧边栏 -->
    <aside
      class="sidebar"
      :class="{ collapsed: appStore.sidebarCollapsed }"
      :style="{ width: appStore.actualSidebarWidth + 'px' }"
    >
      <div class="sidebar-header">
        <div class="logo" @click="goHome">
          <div class="logo-icon-wrapper">
            <img src="/logo.svg" alt="TradingAgents-CN" />
          </div>
          <transition name="fade-slide">
            <span v-show="!appStore.sidebarCollapsed" class="logo-text">
              TradingAgents-CN
            </span>
          </transition>
        </div>
      </div>
      
      <nav class="sidebar-nav">
        <SidebarMenu />
      </nav>
      
      <div class="sidebar-footer">
        <UserProfile />
      </div>
    </aside>

    <!-- 点击蒙层：移动端展开时，点击空白处收起侧边栏 -->
    <transition name="fade">
      <div
        v-if="isMobile && !appStore.sidebarCollapsed"
        class="sidebar-overlay"
        @click="appStore.setSidebarCollapsed(true)"
      ></div>
    </transition>

    <!-- 主内容区 -->
    <div class="main-container" :style="{ marginLeft: mainMarginLeft }" @click="handleMainClick">
      <!-- 顶部导航栏 -->
      <header class="header">
        <div class="header-left">
          <button
            class="sidebar-toggle"
            @click.stop="appStore.toggleSidebar()"
            :title="appStore.sidebarCollapsed ? '展开侧边栏' : '收起侧边栏'"
          >
            <el-icon :class="{ rotated: !appStore.sidebarCollapsed }">
              <Expand />
            </el-icon>
          </button>
          
          <Breadcrumb />
        </div>
        
        <div class="header-right">
          <HeaderActions />
        </div>
      </header>

      <!-- 页面内容 -->
      <main class="main-content">
        <div class="content-wrapper">
          <router-view v-slot="{ Component, route }">
            <transition
              :name="route.meta.transition || 'fade'"
              mode="out-in"
              appear
            >
              <keep-alive :include="keepAliveComponents">
                <component :is="Component" :key="route.fullPath" />
              </keep-alive>
            </transition>
          </router-view>
        </div>
      </main>

      <!-- 页脚 -->
      <footer class="footer">
        <AppFooter />
      </footer>
    </div>

    <!-- 回到顶部 -->
    <el-backtop :right="40" :bottom="40" class="custom-backtop">
      <div class="backtop-inner">
        <el-icon><ArrowUp /></el-icon>
      </div>
    </el-backtop>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useRouter } from 'vue-router'
import { useAppStore } from '@/stores/app'
import SidebarMenu from '@/components/Layout/SidebarMenu.vue'
import UserProfile from '@/components/Layout/UserProfile.vue'
import Breadcrumb from '@/components/Layout/Breadcrumb.vue'
import HeaderActions from '@/components/Layout/HeaderActions.vue'
import AppFooter from '@/components/Layout/AppFooter.vue'
import { Expand, ArrowUp } from '@element-plus/icons-vue'

const router = useRouter()
const appStore = useAppStore()
const route = useRoute()
const { width } = useWindowSize()

// 需要缓存的组件
const keepAliveComponents = computed(() => [
  'Dashboard',
  'StockScreening',
  'AnalysisHistory',
  'QueueManagement'
])

// 移动端判断
const isMobile = computed(() => width.value < 768)

// 主内容区左边距
const mainMarginLeft = computed(() => {
  if (isMobile.value) return '0px'
  return appStore.actualSidebarWidth + 'px'
})

// 点击主内容时，若移动端且侧边栏已展开，则收起
const handleMainClick = () => {
  if (isMobile.value && !appStore.sidebarCollapsed) {
    appStore.setSidebarCollapsed(true)
  }
}

// 点击logo回到首页
const goHome = () => {
  router.push('/dashboard')
}

// 监听窗口大小变化：在小屏幕上自动折叠侧边栏
watch(width, (newWidth) => {
  if (newWidth < 768 && !appStore.sidebarCollapsed) {
    appStore.setSidebarCollapsed(true)
  }
})

// 路由变化时，移动端收起侧边栏
watch(() => route.fullPath, () => {
  if (isMobile.value) {
    appStore.setSidebarCollapsed(true)
  }
})
</script>

<style lang="scss" scoped>
.basic-layout {
  min-height: 100vh;
  background-color: var(--el-bg-color-page);
}

.sidebar-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(4px);
  z-index: 950;
}

.sidebar {
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  background: var(--el-bg-color);
  border-right: 1px solid var(--el-border-color-lighter);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  z-index: 1000;
  display: flex;
  flex-direction: column;
  box-shadow: 2px 0 8px rgba(0, 0, 0, 0.05);

  &.collapsed {
    width: 64px !important;
  }

  .sidebar-header {
    height: 64px;
    display: flex;
    align-items: center;
    padding: 0 16px;
    border-bottom: 1px solid var(--el-border-color-lighter);
    background: linear-gradient(to right, rgba(79, 70, 229, 0.03), transparent);

    .logo {
      display: flex;
      align-items: center;
      gap: 12px;
      cursor: pointer;
      transition: opacity 0.2s ease;

      &:hover {
        opacity: 0.8;
      }

      .logo-icon-wrapper {
        width: 36px;
        height: 36px;
        display: flex;
        align-items: center;
        justify-content: center;
        background: linear-gradient(135deg, #4F46E5 0%, #6366F1 100%);
        border-radius: 10px;
        box-shadow: 0 4px 12px rgba(79, 70, 229, 0.3);
        transition: transform 0.3s ease, box-shadow 0.3s ease;

        &:hover {
          transform: scale(1.05);
          box-shadow: 0 6px 16px rgba(79, 70, 229, 0.4);
        }

        img {
          width: 24px;
          height: 24px;
          filter: brightness(0) invert(1);
        }
      }

      .logo-text {
        font-size: 17px;
        font-weight: 700;
        background: linear-gradient(135deg, #4F46E5 0%, #6366F1 100%);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        background-clip: text;
        white-space: nowrap;
        letter-spacing: -0.5px;
      }
    }
  }

  .sidebar-nav {
    flex: 1;
    overflow-y: auto;
    overflow-x: hidden;
    padding: 8px 0;

    &::-webkit-scrollbar {
      width: 4px;
    }

    &::-webkit-scrollbar-thumb {
      background: rgba(0, 0, 0, 0.1);
      border-radius: 2px;
    }
  }

  .sidebar-footer {
    border-top: 1px solid var(--el-border-color-lighter);
    padding: 12px;
    background: linear-gradient(to right, rgba(79, 70, 229, 0.02), transparent);
  }
}

.main-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  transition: margin-left 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  background: var(--el-bg-color-page);
}

.header {
  height: 64px;
  background: var(--el-bg-color);
  border-bottom: 1px solid var(--el-border-color-lighter);
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 24px;
  position: sticky;
  top: 0;
  z-index: 999;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);

  .header-left {
    display: flex;
    align-items: center;
    gap: 16px;

    .sidebar-toggle {
      width: 36px;
      height: 36px;
      display: flex;
      align-items: center;
      justify-content: center;
      border: none;
      background: transparent;
      border-radius: 8px;
      cursor: pointer;
      transition: all 0.25s ease;
      color: var(--el-text-color-regular);

      &:hover {
        background: rgba(79, 70, 229, 0.08);
        color: var(--el-color-primary);
      }

      .el-icon {
        font-size: 20px;
        transition: transform 0.3s ease;

        &.rotated {
          transform: rotate(180deg);
        }
      }
    }
  }

  .header-right {
    display: flex;
    align-items: center;
    gap: 12px;
  }
}

.main-content {
  flex: 1;
  padding: 24px;
  min-height: calc(100vh - 64px - 56px);

  .content-wrapper {
    max-width: 1440px;
    margin: 0 auto;
  }
}

.footer {
  height: 56px;
  background: var(--el-bg-color);
  border-top: 1px solid var(--el-border-color-lighter);
  display: flex;
  align-items: center;
  justify-content: center;
}

// 自定义回到顶部按钮
.custom-backtop {
  :deep(.el-backtop) {
    width: 44px;
    height: 44px;
    border-radius: 12px;
    background: linear-gradient(135deg, #4F46E5 0%, #6366F1 100%);
    box-shadow: 0 4px 14px rgba(79, 70, 229, 0.4);
    border: none;
    transition: all 0.3s ease;

    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 20px rgba(79, 70, 229, 0.5);
    }
  }

  .backtop-inner {
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    font-size: 20px;
  }
}

// 响应式设计
@media (max-width: 768px) {
  .sidebar {
    transform: translateX(-100%);
    box-shadow: none;
    
    &:not(.collapsed) {
      transform: translateX(0);
      box-shadow: 4px 0 20px rgba(0, 0, 0, 0.15);
    }
  }

  .main-container {
    margin-left: 0 !important;
  }

  .main-content {
    padding: 16px;
  }

  .header {
    padding: 0 16px;
  }
}

// 路由过渡动画
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: all 0.3s ease;
}

.fade-slide-enter-from,
.fade-slide-leave-to {
  opacity: 0;
  transform: translateX(-10px);
}

.slide-left-enter-active,
.slide-left-leave-active {
  transition: all 0.3s ease;
}

.slide-left-enter-from {
  transform: translateX(30px);
  opacity: 0;
}

.slide-left-leave-to {
  transform: translateX(-30px);
  opacity: 0;
}

.slide-up-enter-active,
.slide-up-leave-active {
  transition: all 0.3s ease;
}

.slide-up-enter-from {
  transform: translateY(20px);
  opacity: 0;
}

.slide-up-leave-to {
  transform: translateY(-20px);
  opacity: 0;
}

// 暗色主题适配
html.dark {
  .sidebar {
    background: #141414;
    border-right-color: rgba(255, 255, 255, 0.06);
    box-shadow: 2px 0 8px rgba(0, 0, 0, 0.2);

    .sidebar-header {
      border-bottom-color: rgba(255, 255, 255, 0.06);
      background: linear-gradient(to right, rgba(79, 70, 229, 0.08), transparent);

      .logo .logo-text {
        background: linear-gradient(135deg, #818CF8 0%, #A5B4FC 100%);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        background-clip: text;
      }
    }

    .sidebar-footer {
      border-top-color: rgba(255, 255, 255, 0.06);
      background: linear-gradient(to right, rgba(79, 70, 229, 0.05), transparent);
    }
  }

  .header {
    background: #141414;
    border-bottom-color: rgba(255, 255, 255, 0.06);
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);

    .sidebar-toggle {
      color: #d1d5db;

      &:hover {
        background: rgba(79, 70, 229, 0.15);
        color: #818CF8;
      }
    }
  }

  .footer {
    background: #141414;
    border-top-color: rgba(255, 255, 255, 0.06);
  }

  .sidebar-overlay {
    background: rgba(0, 0, 0, 0.7);
  }
}
</style>
