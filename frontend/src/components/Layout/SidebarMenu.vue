<template>
  <el-menu
    :default-active="activeMenu"
    :collapse="appStore.sidebarCollapsed"
    :unique-opened="true"
    router
    class="sidebar-menu"
  >
    <el-menu-item index="/dashboard" class="menu-item">
      <el-icon><Odometer /></el-icon>
      <template #title>仪表板</template>
    </el-menu-item>

    <el-menu-item index="/learning" class="menu-item">
      <el-icon><Reading /></el-icon>
      <template #title>学习中心</template>
    </el-menu-item>

    <el-sub-menu index="/analysis" class="sub-menu">
      <template #title>
        <el-icon><TrendCharts /></el-icon>
        <span>股票分析</span>
      </template>
      <el-menu-item index="/analysis/single" class="sub-menu-item">单股分析</el-menu-item>
      <el-menu-item index="/analysis/batch" class="sub-menu-item">批量分析</el-menu-item>
      <el-menu-item index="/reports" class="sub-menu-item">分析报告</el-menu-item>
    </el-sub-menu>

    <el-menu-item index="/tasks" class="menu-item">
      <el-icon><List /></el-icon>
      <template #title>任务中心</template>
    </el-menu-item>

    <el-menu-item index="/screening" class="menu-item">
      <el-icon><Search /></el-icon>
      <template #title>股票筛选</template>
    </el-menu-item>

    <el-menu-item index="/favorites" class="menu-item">
      <el-icon><Star /></el-icon>
      <template #title>我的自选股</template>
    </el-menu-item>

    <el-menu-item index="/paper" class="menu-item">
      <el-icon><CreditCard /></el-icon>
      <template #title>模拟交易</template>
    </el-menu-item>

    <!-- 分隔线 -->
    <div class="menu-divider" v-if="!appStore.sidebarCollapsed"></div>

    <el-sub-menu index="/settings" class="sub-menu">
      <template #title>
        <el-icon><Setting /></el-icon>
        <span>设置</span>
      </template>

      <!-- 个人设置 -->
      <el-sub-menu index="/settings-personal">
        <template #title>个人设置</template>
        <el-menu-item index="/settings" class="sub-menu-item">通用设置</el-menu-item>
        <el-menu-item index="/settings?tab=appearance" class="sub-menu-item">外观设置</el-menu-item>
        <el-menu-item index="/settings?tab=analysis" class="sub-menu-item">分析偏好</el-menu-item>
        <el-menu-item index="/settings?tab=notifications" class="sub-menu-item">通知设置</el-menu-item>
        <el-menu-item index="/settings?tab=security" class="sub-menu-item">安全设置</el-menu-item>
      </el-sub-menu>

      <!-- 系统配置 -->
      <el-sub-menu index="/settings-config">
        <template #title>系统配置</template>
        <el-menu-item index="/settings/config" class="sub-menu-item">配置管理</el-menu-item>
        <el-menu-item index="/settings/cache" class="sub-menu-item">缓存管理</el-menu-item>
      </el-sub-menu>

      <!-- 系统管理 -->
      <el-sub-menu index="/settings-admin">
        <template #title>系统管理</template>
        <el-menu-item index="/settings/database" class="sub-menu-item">数据库管理</el-menu-item>
        <el-menu-item index="/settings/logs" class="sub-menu-item">操作日志</el-menu-item>
        <el-menu-item index="/settings/system-logs" class="sub-menu-item">系统日志</el-menu-item>
        <el-menu-item index="/settings/sync" class="sub-menu-item">多数据源同步</el-menu-item>
        <el-menu-item index="/settings/scheduler" class="sub-menu-item">定时任务</el-menu-item>
        <el-menu-item index="/settings/usage" class="sub-menu-item">使用统计</el-menu-item>
      </el-sub-menu>
    </el-sub-menu>

    <el-menu-item index="/about" class="menu-item">
      <el-icon><InfoFilled /></el-icon>
      <template #title>关于</template>
    </el-menu-item>
  </el-menu>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useRoute } from 'vue-router'
import { useAppStore } from '@/stores/app'
import {
  Odometer,
  Reading,
  TrendCharts,
  Search,
  Star,
  List,
  Setting,
  InfoFilled,
  CreditCard
} from '@element-plus/icons-vue'

const route = useRoute()
const appStore = useAppStore()

const activeMenu = computed(() => route.path)
</script>

<style lang="scss" scoped>
.sidebar-menu {
  border: none !important;
  background: transparent !important;
  padding: 8px;

  :deep(.el-menu-item),
  :deep(.el-sub-menu__title) {
    height: 44px;
    line-height: 44px;
    margin: 2px 0;
    border-radius: 10px;
    color: var(--el-text-color-regular);
    transition: all 0.25s ease;
    position: relative;
    overflow: hidden;

    &::before {
      content: '';
      position: absolute;
      left: 0;
      top: 50%;
      transform: translateY(-50%);
      width: 0;
      height: 60%;
      background: linear-gradient(135deg, #4F46E5 0%, #6366F1 100%);
      border-radius: 0 3px 3px 0;
      transition: width 0.25s ease;
    }

    &:hover {
      background: linear-gradient(to right, rgba(79, 70, 229, 0.08), transparent) !important;
      color: var(--el-color-primary);
    }

    .el-icon {
      font-size: 18px;
      margin-right: 10px;
      transition: transform 0.25s ease, color 0.25s ease;
    }
  }

  :deep(.el-menu-item.is-active) {
    background: linear-gradient(to right, rgba(79, 70, 229, 0.12), transparent) !important;
    color: var(--el-color-primary);
    font-weight: 600;

    &::before {
      width: 4px;
    }

    .el-icon {
      color: var(--el-color-primary);
      transform: scale(1.1);
    }
  }

  :deep(.el-sub-menu.is-active > .el-sub-menu__title) {
    color: var(--el-color-primary);
    font-weight: 600;

    .el-icon {
      color: var(--el-color-primary);
    }
  }

  :deep(.el-sub-menu__title:hover) {
    background: linear-gradient(to right, rgba(79, 70, 229, 0.08), transparent) !important;
  }

  // 子菜单项样式
  :deep(.el-sub-menu .el-menu-item) {
    padding-left: 48px !important;
    height: 40px;
    line-height: 40px;
    font-size: 13px;
  }

  // 折叠状态样式
  &.el-menu--collapse {
    width: 48px !important;

    :deep(.el-menu-item),
    :deep(.el-sub-menu__title) {
      padding: 0 !important;
      justify-content: center;

      .el-icon {
        margin-right: 0;
      }
    }

    :deep(.el-sub-menu__icon-arrow) {
      display: none;
    }
  }
}

.menu-divider {
  height: 1px;
  background: linear-gradient(to right, transparent, var(--el-border-color-lighter), transparent);
  margin: 12px 12px;
}

// 子菜单弹出样式
:deep(.el-menu--popup) {
  background: var(--el-bg-color) !important;
  border-radius: 12px !important;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.15) !important;
  border: 1px solid var(--el-border-color-lighter) !important;
  padding: 8px !important;
  min-width: 160px !important;

  .el-menu-item {
    border-radius: 8px;
    margin: 2px 0;
    height: 38px;
    line-height: 38px;

    &:hover {
      background: rgba(79, 70, 229, 0.08) !important;
    }

    &.is-active {
      background: rgba(79, 70, 229, 0.12) !important;
      color: var(--el-color-primary);
    }
  }

  .el-sub-menu__title {
    border-radius: 8px;
    margin: 2px 0;
    height: 38px;
    line-height: 38px;
  }
}

// 暗色主题适配
html.dark {
  .sidebar-menu {
    :deep(.el-menu-item),
    :deep(.el-sub-menu__title) {
      color: #d1d5db;

      &:hover {
        background: linear-gradient(to right, rgba(79, 70, 229, 0.15), transparent) !important;
        color: #818CF8;
      }
    }

    :deep(.el-menu-item.is-active) {
      background: linear-gradient(to right, rgba(79, 70, 229, 0.2), transparent) !important;
      color: #818CF8;

      .el-icon {
        color: #818CF8;
      }
    }

    :deep(.el-sub-menu.is-active > .el-sub-menu__title) {
      color: #818CF8;

      .el-icon {
        color: #818CF8;
      }
    }
  }

  .menu-divider {
    background: linear-gradient(to right, transparent, rgba(255, 255, 255, 0.1), transparent);
  }
}
</style>
