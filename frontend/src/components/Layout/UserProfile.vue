<template>
  <div class="user-profile" :class="{ collapsed: appStore.sidebarCollapsed }">
    <el-dropdown trigger="click" @command="handleCommand" placement="top-start">
      <div class="profile-info">
        <div class="avatar-wrapper">
          <el-avatar :size="36" :src="userAvatar" class="user-avatar">
            <el-icon><User /></el-icon>
          </el-avatar>
          <span class="status-dot"></span>
        </div>
        <transition name="fade-slide">
          <div v-if="!appStore.sidebarCollapsed" class="user-info">
            <div class="username">{{ userDisplayName }}</div>
            <div class="user-role">
              <span class="role-badge">{{ userRole }}</span>
            </div>
          </div>
        </transition>
        <el-icon v-if="!appStore.sidebarCollapsed" class="dropdown-icon"><ArrowUp /></el-icon>
      </div>
      
      <template #dropdown>
        <el-dropdown-menu class="user-dropdown-menu">
          <div class="dropdown-header">
            <el-avatar :size="48" :src="userAvatar" class="header-avatar">
              <el-icon><User /></el-icon>
            </el-avatar>
            <div class="header-info">
              <div class="header-name">{{ userDisplayName }}</div>
              <div class="header-email">{{ userEmail }}</div>
            </div>
          </div>
          <el-dropdown-item command="settings">
            <el-icon><Setting /></el-icon>
            <span>个人设置</span>
          </el-dropdown-item>
          <el-dropdown-item command="theme">
            <el-icon>
              <Sunny v-if="appStore.isDarkTheme" />
              <Moon v-else />
            </el-icon>
            <span>{{ appStore.isDarkTheme ? '切换亮色' : '切换暗色' }}</span>
          </el-dropdown-item>
          <el-dropdown-item divided command="logout" class="logout-item">
            <el-icon><SwitchButton /></el-icon>
            <span>退出登录</span>
          </el-dropdown-item>
        </el-dropdown-menu>
      </template>
    </el-dropdown>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { useAppStore } from '@/stores/app'
import { useAuthStore } from '@/stores/auth'
import { User, Setting, SwitchButton, ArrowUp, Sunny, Moon } from '@element-plus/icons-vue'

const router = useRouter()
const appStore = useAppStore()
const authStore = useAuthStore()

// 用户头像：优先使用用户设置的头像，否则返回 undefined 使用 el-avatar 的默认图标
const userAvatar = computed(() => authStore.user?.avatar || undefined)
const userDisplayName = computed(() => authStore.user?.username || '未登录')
const userEmail = computed(() => authStore.user?.email || '暂无邮箱')
const userRole = computed(() => {
  if (!authStore.user) return '未登录'
  return '用户'
})

const handleCommand = async (command: string) => {
  switch (command) {
    case 'settings':
      router.push('/settings')
      break
    case 'theme':
      appStore.toggleTheme()
      break
    case 'logout':
      await authStore.logout()
      ElMessage.success('已退出登录')
      router.push('/login')
      break
  }
}
</script>

<style lang="scss" scoped>
.user-profile {
  padding: 8px;

  &.collapsed {
    padding: 8px 4px;

    .profile-info {
      justify-content: center;
      padding: 8px;
    }
  }

  .profile-info {
    display: flex;
    align-items: center;
    gap: 12px;
    cursor: pointer;
    padding: 10px 12px;
    border-radius: 12px;
    transition: all 0.25s ease;
    background: linear-gradient(to right, rgba(79, 70, 229, 0.05), transparent);
    border: 1px solid transparent;

    &:hover {
      background: linear-gradient(to right, rgba(79, 70, 229, 0.1), rgba(79, 70, 229, 0.05));
      border-color: rgba(79, 70, 229, 0.2);

      .dropdown-icon {
        transform: rotate(180deg);
        color: var(--el-color-primary);
      }
    }

    .avatar-wrapper {
      position: relative;
      flex-shrink: 0;

      .user-avatar {
        background: linear-gradient(135deg, #4F46E5 0%, #6366F1 100%);
        box-shadow: 0 2px 8px rgba(79, 70, 229, 0.3);
        transition: transform 0.25s ease;

        &:hover {
          transform: scale(1.05);
        }
      }

      .status-dot {
        position: absolute;
        bottom: 0;
        right: 0;
        width: 10px;
        height: 10px;
        background: linear-gradient(135deg, #10B981 0%, #34D399 100%);
        border: 2px solid var(--el-bg-color);
        border-radius: 50%;
        box-shadow: 0 2px 4px rgba(16, 185, 129, 0.4);
      }
    }

    .user-info {
      flex: 1;
      min-width: 0;

      .username {
        font-size: 14px;
        font-weight: 600;
        color: var(--el-text-color-primary);
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        margin-bottom: 4px;
      }

      .user-role {
        .role-badge {
          display: inline-block;
          padding: 2px 8px;
          font-size: 11px;
          font-weight: 500;
          color: var(--el-color-primary);
          background: rgba(79, 70, 229, 0.1);
          border-radius: 10px;
        }
      }
    }

    .dropdown-icon {
      font-size: 14px;
      color: var(--el-text-color-placeholder);
      transition: all 0.25s ease;
    }
  }
}

// 下拉菜单样式
:deep(.user-dropdown-menu) {
  padding: 8px !important;
  min-width: 220px !important;
  border-radius: 12px !important;

  .dropdown-header {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 16px 12px;
    margin-bottom: 8px;
    border-bottom: 1px solid var(--el-border-color-lighter);

    .header-avatar {
      background: linear-gradient(135deg, #4F46E5 0%, #6366F1 100%);
      box-shadow: 0 4px 12px rgba(79, 70, 229, 0.3);
    }

    .header-info {
      flex: 1;

      .header-name {
        font-size: 15px;
        font-weight: 600;
        color: var(--el-text-color-primary);
        margin-bottom: 4px;
      }

      .header-email {
        font-size: 12px;
        color: var(--el-text-color-secondary);
      }
    }
  }

  .el-dropdown-menu__item {
    padding: 10px 12px !important;
    border-radius: 8px !important;
    margin: 2px 0 !important;
    font-size: 14px !important;
    transition: all 0.2s ease !important;

    .el-icon {
      margin-right: 10px;
      font-size: 16px;
    }

    &:hover {
      background: rgba(79, 70, 229, 0.08) !important;
      color: var(--el-color-primary) !important;
    }

    &.logout-item {
      color: var(--el-color-danger);

      &:hover {
        background: rgba(239, 68, 68, 0.08) !important;
        color: var(--el-color-danger) !important;
      }
    }
  }
}

// 过渡动画
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: all 0.25s ease;
}

.fade-slide-enter-from,
.fade-slide-leave-to {
  opacity: 0;
  transform: translateX(-10px);
}

// 暗色主题适配
html.dark {
  .user-profile {
    .profile-info {
      background: linear-gradient(to right, rgba(129, 140, 248, 0.08), transparent);

      &:hover {
        background: linear-gradient(to right, rgba(129, 140, 248, 0.15), rgba(129, 140, 248, 0.08));
        border-color: rgba(129, 140, 248, 0.3);
      }

      .avatar-wrapper {
        .status-dot {
          border-color: #1a1a1a;
        }
      }

      .user-info {
        .user-role {
          .role-badge {
            color: #A5B4FC;
            background: rgba(129, 140, 248, 0.15);
          }
        }
      }
    }
  }
}
</style>
