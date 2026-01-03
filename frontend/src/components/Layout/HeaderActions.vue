<template>
  <div class="header-actions">
    <!-- 主题切换 -->
    <el-tooltip content="切换主题" placement="bottom" :show-after="300">
      <button class="action-btn theme-btn" @click="toggleTheme">
        <transition name="rotate-fade" mode="out-in">
          <el-icon :key="appStore.isDarkTheme ? 'dark' : 'light'">
            <Sunny v-if="appStore.isDarkTheme" />
            <Moon v-else />
          </el-icon>
        </transition>
      </button>
    </el-tooltip>

    <!-- 全屏切换 -->
    <el-tooltip content="全屏" placement="bottom" :show-after="300">
      <button class="action-btn" @click="toggleFullscreen">
        <el-icon><FullScreen /></el-icon>
      </button>
    </el-tooltip>

    <!-- 通知 -->
    <el-tooltip content="通知" placement="bottom" :show-after="300">
      <button class="action-btn notification-btn" @click="openDrawer">
        <el-icon><Bell /></el-icon>
        <span v-if="unreadCount > 0" class="notification-badge">
          {{ unreadCount > 99 ? '99+' : unreadCount }}
        </span>
      </button>
    </el-tooltip>

    <!-- 帮助 -->
    <el-tooltip content="帮助" placement="bottom" :show-after="300">
      <button class="action-btn" @click="showHelp">
        <el-icon><QuestionFilled /></el-icon>
      </button>
    </el-tooltip>

    <!-- 通知抽屉 -->
    <el-drawer 
      v-model="drawerVisible" 
      direction="rtl" 
      size="380px" 
      :with-header="true" 
      title="消息中心"
      class="notification-drawer"
    >
      <template #header>
        <div class="drawer-header">
          <div class="drawer-title">
            <el-icon><Bell /></el-icon>
            <span>消息中心</span>
          </div>
          <el-badge :value="unreadCount" :hidden="unreadCount === 0" type="primary" />
        </div>
      </template>

      <div class="notif-toolbar">
        <el-segmented 
          v-model="filter" 
          :options="[{label: '全部', value: 'all'}, {label: '未读', value: 'unread'}]" 
          size="small" 
        />
        <el-button 
          size="small" 
          text 
          type="primary" 
          @click="onMarkAllRead" 
          :disabled="unreadCount===0"
        >
          全部已读
        </el-button>
      </div>

      <el-scrollbar max-height="calc(100vh - 180px)">
        <transition-group name="list" tag="div" class="notif-list">
          <div v-if="items.length === 0" key="empty" class="empty-state">
            <el-icon class="empty-icon"><Bell /></el-icon>
            <p>暂无通知</p>
          </div>
          <div 
            v-else
            v-for="n in items" 
            :key="n.id" 
            class="notif-item" 
            :class="{unread: n.status==='unread'}"
          >
            <div class="notif-header">
              <el-tag :type="tagType(n.type)" size="small" class="type-tag">
                {{ typeLabel(n.type) }}
              </el-tag>
              <span class="time">{{ toLocal(n.created_at) }}</span>
            </div>
            <div class="notif-title" @click="go(n)">{{ n.title }}</div>
            <div class="notif-content" v-if="n.content">{{ n.content }}</div>
            <div class="notif-actions">
              <el-button 
                size="small" 
                type="primary" 
                link 
                @click="go(n)" 
                :disabled="!n.link"
              >
                查看详情
              </el-button>
              <el-button 
                size="small" 
                link 
                @click="onMarkRead(n)" 
                v-if="n.status==='unread'"
              >
                标记已读
              </el-button>
            </div>
          </div>
        </transition-group>
      </el-scrollbar>
    </el-drawer>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue'
import { useAppStore } from '@/stores/app'
import { useNotificationStore } from '@/stores/notifications'
import { useAuthStore } from '@/stores/auth'
import { storeToRefs } from 'pinia'
import {
  Sunny,
  Moon,
  FullScreen,
  Bell,
  QuestionFilled
} from '@element-plus/icons-vue'

const appStore = useAppStore()
const authStore = useAuthStore()
const notifStore = useNotificationStore()
const { unreadCount, items } = storeToRefs(notifStore)
const drawerVisible = ref(false)
const filter = ref<'all' | 'unread'>('all')
let timerCount: any = null
let timerList: any = null

const toggleTheme = () => { appStore.toggleTheme() }
const toggleFullscreen = () => {
  if (document.fullscreenElement) document.exitFullscreen()
  else document.documentElement.requestFullscreen()
}

function openDrawer() {
  drawerVisible.value = true
  notifStore.loadList(filter.value)
}
function onMarkRead(n: any) { notifStore.markRead(n.id) }
function onMarkAllRead() { notifStore.markAllRead() }
function typeLabel(t: string) { return t === 'analysis' ? '分析' : t === 'alert' ? '预警' : '系统' }
function tagType(t: string) { return t === 'analysis' ? 'success' : t === 'alert' ? 'warning' : 'info' }
function toLocal(iso: string) { try { return new Date(iso).toLocaleString() } catch { return iso } }
function go(n: any) { if (n.link) window.open(n.link, '_blank') }

onMounted(() => {
  notifStore.refreshUnreadCount()
  notifStore.connect()

  timerCount = setInterval(() => notifStore.refreshUnreadCount(), 30000)
  watch(drawerVisible, (v) => {
    if (v) {
      notifStore.loadList(filter.value)
      timerList = setInterval(() => notifStore.loadList(filter.value), 60000)
    } else if (timerList) {
      clearInterval(timerList)
      timerList = null
    }
  }, { immediate: true })
  watch(filter, () => { if (drawerVisible.value) notifStore.loadList(filter.value) })

  watch(() => authStore.token, () => {
    notifStore.connect()
  })
})

onUnmounted(() => {
  if (timerCount) clearInterval(timerCount)
  if (timerList) clearInterval(timerList)
  notifStore.disconnect()
})

function showHelp() {
  window.open('https://mp.weixin.qq.com/s/ppsYiBncynxlsfKFG8uEbw', '_blank')
}
</script>

<style lang="scss" scoped>
.header-actions {
  display: flex;
  align-items: center;
  gap: 8px;

  .action-btn {
    width: 40px;
    height: 40px;
    border-radius: 10px;
    border: none;
    background: transparent;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: all 0.25s ease;
    color: var(--el-text-color-regular);
    position: relative;

    &:hover {
      background: rgba(79, 70, 229, 0.08);
      color: var(--el-color-primary);
      transform: translateY(-1px);
    }

    &:active {
      transform: translateY(0);
    }

    .el-icon {
      font-size: 20px;
    }

    &.theme-btn {
      .el-icon {
        transition: transform 0.3s ease;
      }

      &:hover .el-icon {
        transform: rotate(15deg);
      }
    }

    &.notification-btn {
      .notification-badge {
        position: absolute;
        top: 4px;
        right: 4px;
        min-width: 18px;
        height: 18px;
        padding: 0 5px;
        font-size: 11px;
        font-weight: 600;
        line-height: 18px;
        text-align: center;
        color: white;
        background: linear-gradient(135deg, #EF4444 0%, #F87171 100%);
        border-radius: 10px;
        box-shadow: 0 2px 6px rgba(239, 68, 68, 0.4);
        animation: badge-pulse 2s ease-in-out infinite;
      }
    }
  }
}

@keyframes badge-pulse {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
}

// 主题切换动画
.rotate-fade-enter-active,
.rotate-fade-leave-active {
  transition: all 0.3s ease;
}

.rotate-fade-enter-from {
  opacity: 0;
  transform: rotate(-90deg) scale(0.5);
}

.rotate-fade-leave-to {
  opacity: 0;
  transform: rotate(90deg) scale(0.5);
}

// 通知抽屉样式
:deep(.notification-drawer) {
  .el-drawer__header {
    padding: 20px 24px;
    margin-bottom: 0;
    border-bottom: 1px solid var(--el-border-color-lighter);
  }

  .el-drawer__body {
    padding: 16px 20px;
  }
}

.drawer-header {
  display: flex;
  align-items: center;
  gap: 12px;

  .drawer-title {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 18px;
    font-weight: 600;
    color: var(--el-text-color-primary);

    .el-icon {
      color: var(--el-color-primary);
    }
  }
}

.notif-toolbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 16px;
  padding-bottom: 16px;
  border-bottom: 1px solid var(--el-border-color-lighter);
}

.notif-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 48px 24px;
  color: var(--el-text-color-secondary);

  .empty-icon {
    font-size: 48px;
    margin-bottom: 12px;
    opacity: 0.5;
  }

  p {
    margin: 0;
    font-size: 14px;
  }
}

.notif-item {
  padding: 16px;
  border-radius: 12px;
  border: 1px solid var(--el-border-color-lighter);
  background: var(--el-bg-color);
  transition: all 0.25s ease;

  &:hover {
    border-color: var(--el-color-primary-light-5);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  }

  &.unread {
    background: linear-gradient(to right, rgba(79, 70, 229, 0.05), transparent);
    border-left: 3px solid var(--el-color-primary);
  }

  .notif-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;

    .type-tag {
      font-weight: 500;
    }

    .time {
      font-size: 12px;
      color: var(--el-text-color-placeholder);
    }
  }

  .notif-title {
    font-size: 14px;
    font-weight: 600;
    color: var(--el-text-color-primary);
    cursor: pointer;
    margin-bottom: 6px;
    line-height: 1.5;
    transition: color 0.2s ease;

    &:hover {
      color: var(--el-color-primary);
    }
  }

  .notif-content {
    font-size: 13px;
    color: var(--el-text-color-secondary);
    line-height: 1.5;
    margin-bottom: 12px;
  }

  .notif-actions {
    display: flex;
    gap: 12px;
    padding-top: 10px;
    border-top: 1px solid var(--el-border-color-lighter);
  }
}

// 列表动画
.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(20px);
}

// 暗色主题适配
html.dark {
  .header-actions {
    .action-btn {
      &:hover {
        background: rgba(129, 140, 248, 0.15);
        color: #818CF8;
      }

      &.notification-btn {
        .notification-badge {
          box-shadow: 0 2px 6px rgba(239, 68, 68, 0.5);
        }
      }
    }
  }

  .notif-item {
    &:hover {
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
    }

    &.unread {
      background: linear-gradient(to right, rgba(129, 140, 248, 0.1), transparent);
    }
  }
}
</style>
