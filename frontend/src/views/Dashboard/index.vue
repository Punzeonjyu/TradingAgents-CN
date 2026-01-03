<template>
  <div class="dashboard">
    <!-- 欢迎区域 -->
    <div class="welcome-section">
      <div class="welcome-bg">
        <div class="bg-shape shape-1"></div>
        <div class="bg-shape shape-2"></div>
        <div class="bg-shape shape-3"></div>
      </div>
      <div class="welcome-content">
        <h1 class="welcome-title">
          欢迎使用 TradingAgents-CN
          <span class="version-badge">v1.0.0-preview</span>
        </h1>
        <p class="welcome-subtitle">
          现代化的多智能体股票分析学习平台，辅助你掌握更全面的市场视角分析股票
        </p>
      </div>
      <div class="welcome-actions">
        <el-button type="primary" size="large" class="action-btn primary-btn" @click="quickAnalysis">
          <el-icon><TrendCharts /></el-icon>
          快速分析
        </el-button>
        <el-button size="large" class="action-btn secondary-btn" @click="goToScreening">
          <el-icon><Search /></el-icon>
          股票筛选
        </el-button>
      </div>
    </div>

    <!-- 学习中心推荐卡片 -->
    <el-card class="learning-highlight-card">
      <div class="learning-highlight">
        <div class="learning-icon">
          <el-icon size="40"><Reading /></el-icon>
        </div>
        <div class="learning-content">
          <h2>📚 AI股票分析学习中心</h2>
          <p>从零开始学习AI、大语言模型和智能股票分析。了解多智能体系统如何协作分析股票，掌握提示词工程技巧，选择合适的大模型，理解AI的能力与局限性。</p>
          <div class="learning-features">
            <span class="feature-tag">🤖 AI基础知识</span>
            <span class="feature-tag">✍️ 提示词工程</span>
            <span class="feature-tag">🎯 模型选择</span>
            <span class="feature-tag">📊 分析原理</span>
            <span class="feature-tag">⚠️ 风险认知</span>
            <span class="feature-tag">🎓 实战教程</span>
          </div>
        </div>
        <div class="learning-action">
          <el-button type="primary" size="large" @click="goToLearning">
            <el-icon><Reading /></el-icon>
            开始学习
          </el-button>
        </div>
      </div>
    </el-card>

    <!-- 主要功能区域 -->
    <el-row :gutter="24" class="main-content">
      <!-- 左侧：快速操作 -->
      <el-col :xs="24" :sm="24" :md="16" :lg="16">
        <el-card class="quick-actions-card">
          <template #header>
            <div class="card-header-title">
              <span class="title-icon">⚡</span>
              快速操作
            </div>
          </template>
          <div class="quick-actions">
            <div class="action-item" @click="goToSingleAnalysis">
              <div class="action-icon gradient-blue">
                <el-icon><Document /></el-icon>
              </div>
              <div class="action-content">
                <h3>单股分析</h3>
                <p>深度分析单只股票的投资价值</p>
              </div>
              <el-icon class="action-arrow"><ArrowRight /></el-icon>
            </div>

            <div class="action-item" @click="goToBatchAnalysis">
              <div class="action-icon gradient-purple">
                <el-icon><Files /></el-icon>
              </div>
              <div class="action-content">
                <h3>批量分析</h3>
                <p>同时分析多只股票，提高效率</p>
              </div>
              <el-icon class="action-arrow"><ArrowRight /></el-icon>
            </div>

            <div class="action-item" @click="goToScreening">
              <div class="action-icon gradient-green">
                <el-icon><Search /></el-icon>
              </div>
              <div class="action-content">
                <h3>股票筛选</h3>
                <p>通过多维度条件筛选优质股票</p>
              </div>
              <el-icon class="action-arrow"><ArrowRight /></el-icon>
            </div>

            <div class="action-item" @click="goToQueue">
              <div class="action-icon gradient-orange">
                <el-icon><List /></el-icon>
              </div>
              <div class="action-content">
                <h3>任务中心</h3>
                <p>查看和管理分析任务列表</p>
              </div>
              <el-icon class="action-arrow"><ArrowRight /></el-icon>
            </div>
          </div>
        </el-card>

        <!-- 最近分析 -->
        <el-card class="recent-analyses-card" style="margin-top: 24px;">
          <template #header>
            <div class="card-header-title">
              <span class="title-icon">📊</span>
              最近分析
            </div>
          </template>
          <el-table :data="recentAnalyses" style="width: 100%" class="custom-table">
            <el-table-column prop="stock_code" label="股票代码" width="120">
              <template #default="{ row }">
                <span class="stock-code-cell">{{ row.stock_code }}</span>
              </template>
            </el-table-column>
            <el-table-column prop="stock_name" label="股票名称" width="150" />
            <el-table-column prop="status" label="状态" width="100">
              <template #default="{ row }">
                <el-tag :type="getStatusType(row.status)" class="status-tag">
                  {{ getStatusText(row.status) }}
                </el-tag>
              </template>
            </el-table-column>
            <el-table-column prop="start_time" label="创建时间" width="180">
              <template #default="{ row }">
                <span class="time-cell">{{ formatTime(row.start_time) }}</span>
              </template>
            </el-table-column>
            <el-table-column label="操作">
              <template #default="{ row }">
                <el-button type="primary" link size="small" @click="viewAnalysis(row)">
                  查看
                </el-button>
                <el-button
                  v-if="row.status === 'completed'"
                  type="success" link
                  size="small"
                  @click="downloadReport(row)"
                >
                  下载
                </el-button>
              </template>
            </el-table-column>
          </el-table>

          <div class="table-footer">
            <el-button type="primary" link @click="goToHistory">
              查看全部历史 <el-icon><ArrowRight /></el-icon>
            </el-button>
          </div>
        </el-card>

        <!-- 市场快讯 -->
        <el-card class="market-news-card" style="margin-top: 24px;">
          <template #header>
            <div class="card-header-title">
              <span class="title-icon">📰</span>
              市场快讯
            </div>
          </template>
          <div v-if="marketNews.length > 0" class="news-list">
            <div
              v-for="news in marketNews"
              :key="news.id"
              class="news-item"
              @click="openNewsUrl(news.url)"
            >
              <div class="news-content">
                <div class="news-title">{{ news.title }}</div>
                <div class="news-meta">
                  <span class="news-source" v-if="news.source">{{ news.source }}</span>
                  <span class="news-time">{{ formatTime(news.time) }}</span>
                </div>
              </div>
              <el-icon class="news-arrow"><ArrowRight /></el-icon>
            </div>
          </div>
          <div v-else class="empty-state">
            <el-icon class="empty-icon"><InfoFilled /></el-icon>
            <p>暂无市场快讯</p>
          </div>
        </el-card>
      </el-col>

      <!-- 右侧：自选股和快讯 -->
      <el-col :xs="24" :sm="24" :md="8" :lg="8">
        <!-- 我的自选股 -->
        <el-card class="favorites-card">
          <template #header>
            <div class="card-header">
              <div class="card-header-title">
                <span class="title-icon">⭐</span>
                我的自选股
              </div>
              <el-button type="primary" link size="small" @click="goToFavorites">
                查看全部 <el-icon><ArrowRight /></el-icon>
              </el-button>
            </div>
          </template>

          <div v-if="favoriteStocks.length === 0" class="empty-favorites">
            <el-empty description="暂无自选股" :image-size="60">
              <el-button type="primary" size="small" @click="goToFavorites">
                添加自选股
              </el-button>
            </el-empty>
          </div>

          <div v-else class="favorites-list">
            <div
              v-for="stock in favoriteStocks.slice(0, 5)"
              :key="stock.stock_code"
              class="favorite-item"
              @click="viewStockDetail(stock)"
            >
              <div class="stock-info">
                <div class="stock-code">{{ stock.stock_code }}</div>
                <div class="stock-name">{{ stock.stock_name }}</div>
              </div>
              <div class="stock-price">
                <div class="current-price">¥{{ stock.current_price }}</div>
                <div
                  class="change-percent"
                  :class="getPriceChangeClass(stock.change_percent)"
                >
                  {{ stock.change_percent > 0 ? '+' : '' }}{{ Number(stock.change_percent).toFixed(2) }}%
                </div>
              </div>
            </div>
          </div>

          <div v-if="favoriteStocks.length > 5" class="favorites-footer">
            <el-button type="primary" link size="small" @click="goToFavorites">
              查看全部 {{ favoriteStocks.length }} 只自选股
            </el-button>
          </div>
        </el-card>

        <!-- 模拟交易账户 -->
        <el-card class="paper-trading-card" style="margin-top: 24px;">
          <template #header>
            <div class="card-header">
              <div class="card-header-title">
                <span class="title-icon">💰</span>
                模拟交易账户
              </div>
              <el-button type="primary" link size="small" @click="goToPaperTrading">
                查看详情 <el-icon><ArrowRight /></el-icon>
              </el-button>
            </div>
          </template>

          <div v-if="paperAccount" class="paper-account-info">
            <!-- A股账户 -->
            <div class="account-section">
              <div class="account-section-title">🇨🇳 A股账户</div>
              <div class="account-item">
                <div class="account-label">现金</div>
                <div class="account-value">¥{{ formatMoney(paperAccount.cash?.CNY || paperAccount.cash) }}</div>
              </div>
              <div class="account-item">
                <div class="account-label">持仓市值</div>
                <div class="account-value">¥{{ formatMoney(paperAccount.positions_value?.CNY || paperAccount.positions_value) }}</div>
              </div>
              <div class="account-item">
                <div class="account-label">总资产</div>
                <div class="account-value primary">¥{{ formatMoney(paperAccount.equity?.CNY || paperAccount.equity) }}</div>
              </div>
            </div>

            <!-- 港股账户 -->
            <div class="account-section" v-if="paperAccount.cash?.HKD !== undefined">
              <div class="account-section-title">🇭🇰 港股账户</div>
              <div class="account-item">
                <div class="account-label">现金</div>
                <div class="account-value">HK${{ formatMoney(paperAccount.cash.HKD) }}</div>
              </div>
              <div class="account-item">
                <div class="account-label">持仓市值</div>
                <div class="account-value">HK${{ formatMoney(paperAccount.positions_value?.HKD || 0) }}</div>
              </div>
              <div class="account-item">
                <div class="account-label">总资产</div>
                <div class="account-value primary">HK${{ formatMoney(paperAccount.equity?.HKD || 0) }}</div>
              </div>
            </div>

            <!-- 美股账户 -->
            <div class="account-section" v-if="paperAccount.cash?.USD !== undefined">
              <div class="account-section-title">🇺🇸 美股账户</div>
              <div class="account-item">
                <div class="account-label">现金</div>
                <div class="account-value">${{ formatMoney(paperAccount.cash.USD) }}</div>
              </div>
              <div class="account-item">
                <div class="account-label">持仓市值</div>
                <div class="account-value">${{ formatMoney(paperAccount.positions_value?.USD || 0) }}</div>
              </div>
              <div class="account-item">
                <div class="account-label">总资产</div>
                <div class="account-value primary">${{ formatMoney(paperAccount.equity?.USD || 0) }}</div>
              </div>
            </div>
          </div>

          <div v-else class="empty-state">
            <el-icon class="empty-icon"><InfoFilled /></el-icon>
            <p>暂无账户信息</p>
            <el-button type="primary" size="small" @click="goToPaperTrading">
              查看模拟交易
            </el-button>
          </div>
        </el-card>

        <!-- 多数据源同步 -->
        <MultiSourceSyncCard style="margin-top: 24px;" />
      </el-col>
    </el-row>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '@/stores/auth'
import {
  TrendCharts,
  Search,
  Document,
  Files,
  List,
  ArrowRight,
  InfoFilled,
  Reading
} from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'
import type { AnalysisTask, AnalysisStatus } from '@/types/analysis'
import MultiSourceSyncCard from '@/components/Dashboard/MultiSourceSyncCard.vue'
import { favoritesApi } from '@/api/favorites'
import { analysisApi } from '@/api/analysis'
import { newsApi } from '@/api/news'
import { paperApi, type PaperAccountSummary } from '@/api/paper'

const router = useRouter()
const authStore = useAuthStore()

// 响应式数据
const userStats = ref({
  totalAnalyses: 0,
  successfulAnalyses: 0,
  dailyQuota: 1000,
  dailyUsed: 0,
  concurrentLimit: 3
})

const systemStatus = ref({
  api: true,
  queue: true,
  database: true
})

const queueStats = ref({
  pending: 0,
  processing: 0,
  completed: 0,
  failed: 0
})

const recentAnalyses = ref<AnalysisTask[]>([])

// 自选股数据
const favoriteStocks = ref<any[]>([])

// 市场快讯数据
const marketNews = ref<any[]>([])
const syncingNews = ref(false)

// 模拟交易账户数据
const paperAccount = ref<PaperAccountSummary | null>(null)



// 方法
const quickAnalysis = () => {
  router.push('/analysis/single')
}

const goToSingleAnalysis = () => {
  router.push('/analysis/single')
}

const goToBatchAnalysis = () => {
  router.push('/analysis/batch')
}

const goToScreening = () => {
  router.push('/screening')
}

const goToQueue = () => {
  router.push('/queue')
}

const goToHistory = () => {
  router.push('/tasks?tab=completed')
}

const goToLearning = () => {
  router.push('/learning')
}

const viewAnalysis = (analysis: AnalysisTask) => {
  const status = (analysis as any)?.status
  if (status === 'completed') {
    router.push({ name: 'ReportDetail', params: { id: analysis.task_id } })
  } else {
    // 未完成任务跳转到任务中心的"进行中"标签页
    router.push('/tasks?tab=running')
  }
}

const downloadReport = async (analysis: AnalysisTask) => {
  try {
    const reportId = analysis.task_id
    const res = await fetch(`/api/reports/${reportId}/download?format=markdown`, {
      headers: {
        'Authorization': `Bearer ${authStore.token}`
      }
    })
    if (!res.ok) {
      const msg = `下载失败：HTTP ${res.status}`
      console.error(msg)
      ElMessage.error('下载失败，报告可能尚未生成')
      return
    }
    const blob = await res.blob()
    const url = window.URL.createObjectURL(blob)
    const a = document.createElement('a')
    a.href = url
    const code = (analysis as any).stock_code || (analysis as any).stock_symbol || 'stock'
    const dateStr = (analysis as any).analysis_date || (analysis as any).start_time || ''
    a.download = `${code}_分析报告_${String(dateStr).slice(0,10)}.md`
    document.body.appendChild(a)
    a.click()
    window.URL.revokeObjectURL(url)
    document.body.removeChild(a)
    ElMessage.success('报告已开始下载')
  } catch (err) {
    console.error('下载报告出错:', err)
    ElMessage.error('下载失败，请稍后重试')
  }
}

const openNewsUrl = (url?: string) => {
  if (url) {
    window.open(url, '_blank')
  } else {
    ElMessage.info('该新闻暂无详情链接')
  }
}

const getStatusType = (status: string | AnalysisStatus): 'success' | 'info' | 'warning' | 'danger' => {
  const statusMap: Record<string, 'success' | 'info' | 'warning' | 'danger'> = {
    pending: 'info',
    processing: 'warning',
    running: 'warning',
    completed: 'success',
    failed: 'danger',
    cancelled: 'info'
  }
  return statusMap[status] || 'info'
}

const getStatusText = (status: string | AnalysisStatus) => {
  const statusMap: Record<string, string> = {
    pending: '等待中',
    processing: '处理中',
    running: '处理中',
    completed: '已完成',
    failed: '失败',
    cancelled: '已取消'
  }
  return statusMap[status] || String(status)
}

import { formatDateTime } from '@/utils/datetime'

const formatTime = (time: string) => {
  return formatDateTime(time)
}

// 自选股相关方法
const goToFavorites = () => {
  router.push('/favorites')
}

const viewStockDetail = (stock: any) => {
  router.push(`/analysis/single?stock_code=${stock.stock_code}`)
}

const getPriceChangeClass = (changePercent: number) => {
  if (changePercent > 0) return 'price-up'
  if (changePercent < 0) return 'price-down'
  return 'price-neutral'
}

const loadFavoriteStocks = async () => {
  try {
    const response = await favoritesApi.list()
    if (response.success && response.data) {
      favoriteStocks.value = response.data.map((item: any) => ({
        stock_code: item.stock_code,
        stock_name: item.stock_name,
        current_price: item.current_price || 0,
        change_percent: item.change_percent || 0
      }))
    }
  } catch (error) {
    console.error('加载自选股失败:', error)
  }
}

const loadRecentAnalyses = async () => {
  try {
    const res = await analysisApi.getTaskList({
      limit: 10,
      offset: 0,
      status: undefined
    })

    const body: any = (res as any)?.data?.data || (res as any)?.data || res || {}
    const tasks = body.tasks || []

    recentAnalyses.value = tasks
    userStats.value.totalAnalyses = body.total ?? tasks.length
    userStats.value.successfulAnalyses = tasks.filter((item: any) => item.status === 'completed').length
  } catch (error) {
    console.error('加载最近分析失败:', error)
    recentAnalyses.value = []
  }
}

const loadMarketNews = async () => {
  try {
    let response = await newsApi.getLatestNews(undefined, 10, 24)

    if (response.success && response.data && response.data.news.length === 0) {
      console.log('最近 24 小时没有新闻，获取最新的 10 条新闻（不限时间）')
      response = await newsApi.getLatestNews(undefined, 10, 24 * 365)
    }

    if (response.success && response.data) {
      marketNews.value = response.data.news.map((item: any) => ({
        id: item.id || item.title,
        title: item.title,
        time: item.publish_time,
        url: item.url,
        source: item.source
      }))
    }
  } catch (error) {
    console.error('加载市场快讯失败:', error)
    marketNews.value = []
  }
}

// 加载模拟交易账户信息
const loadPaperAccount = async () => {
  try {
    const response = await paperApi.getAccount()
    if (response.success && response.data) {
      paperAccount.value = response.data.account
    }
  } catch (error) {
    console.error('加载模拟交易账户失败:', error)
    paperAccount.value = null
  }
}

// 跳转到模拟交易页面
const goToPaperTrading = () => {
  router.push('/paper')
}

// 格式化金额
const formatMoney = (value: number) => {
  return value.toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ',')
}

// 获取盈亏样式类
const getPnlClass = (pnl: number) => {
  if (pnl > 0) return 'price-up'
  if (pnl < 0) return 'price-down'
  return 'price-neutral'
}

const syncMarketNews = async () => {
  try {
    syncingNews.value = true
    ElMessage.info('正在同步市场新闻，请稍候...')

    const response = await newsApi.syncMarketNews(24, 50)

    if (response.success) {
      ElMessage.success('新闻同步任务已启动，请稍后刷新查看')

      setTimeout(async () => {
        await loadMarketNews()
        if (marketNews.value.length > 0) {
          ElMessage.success(`成功加载 ${marketNews.value.length} 条市场新闻`)
        }
      }, 3000)
    }
  } catch (error) {
    console.error('同步市场快讯失败:', error)
    ElMessage.error('同步市场新闻失败，请稍后重试')
  } finally {
    syncingNews.value = false
  }
}

// 生命周期
onMounted(async () => {
  await loadFavoriteStocks()
  await loadRecentAnalyses()
  await loadMarketNews()
  await loadPaperAccount()
})
</script>

<style lang="scss" scoped>
.dashboard {
  // 欢迎区域
  .welcome-section {
    position: relative;
    background: linear-gradient(135deg, #4F46E5 0%, #7C3AED 50%, #EC4899 100%);
    border-radius: 20px;
    padding: 48px 40px;
    color: white;
    margin-bottom: 24px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    overflow: hidden;
    box-shadow: 0 20px 40px rgba(79, 70, 229, 0.3);

    .welcome-bg {
      position: absolute;
      inset: 0;
      overflow: hidden;

      .bg-shape {
        position: absolute;
        border-radius: 50%;
        background: rgba(255, 255, 255, 0.1);
        animation: float 20s ease-in-out infinite;

        &.shape-1 {
          width: 300px;
          height: 300px;
          top: -100px;
          right: -50px;
          animation-delay: 0s;
        }

        &.shape-2 {
          width: 200px;
          height: 200px;
          bottom: -80px;
          left: 20%;
          animation-delay: -5s;
        }

        &.shape-3 {
          width: 150px;
          height: 150px;
          top: 50%;
          right: 30%;
          animation-delay: -10s;
        }
      }
    }

    @keyframes float {
      0%, 100% { transform: translate(0, 0) scale(1); }
      25% { transform: translate(20px, -20px) scale(1.05); }
      50% { transform: translate(-10px, 10px) scale(0.95); }
      75% { transform: translate(-20px, -10px) scale(1.02); }
    }

    .welcome-content {
      position: relative;
      z-index: 1;

      .welcome-title {
        font-size: 36px;
        font-weight: 700;
        margin: 0 0 16px 0;
        display: flex;
        align-items: center;
        gap: 16px;
        text-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);

        .version-badge {
          background: rgba(255, 255, 255, 0.2);
          backdrop-filter: blur(10px);
          padding: 6px 16px;
          border-radius: 20px;
          font-size: 14px;
          font-weight: 500;
          border: 1px solid rgba(255, 255, 255, 0.3);
        }
      }

      .welcome-subtitle {
        font-size: 18px;
        opacity: 0.9;
        margin: 0;
        max-width: 500px;
        line-height: 1.6;
      }
    }

    .welcome-actions {
      position: relative;
      z-index: 1;
      display: flex;
      gap: 16px;

      .action-btn {
        height: 48px;
        padding: 0 28px;
        border-radius: 12px;
        font-size: 16px;
        font-weight: 600;
        transition: all 0.3s ease;

        &.primary-btn {
          background: white !important;
          color: #4F46E5 !important;
          border: none !important;
          box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2) !important;

          &:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.3) !important;
          }
        }

        &.secondary-btn {
          background: rgba(255, 255, 255, 0.15) !important;
          color: white !important;
          border: 1px solid rgba(255, 255, 255, 0.3) !important;
          backdrop-filter: blur(10px);

          &:hover {
            background: rgba(255, 255, 255, 0.25) !important;
            transform: translateY(-2px);
          }
        }
      }
    }
  }

  // 学习中心卡片
  .learning-highlight-card {
    margin-bottom: 24px;
    border: 2px solid transparent;
    background: linear-gradient(var(--el-bg-color), var(--el-bg-color)) padding-box,
                linear-gradient(135deg, #4F46E5, #7C3AED, #EC4899) border-box;
    border-radius: 16px !important;

    &:hover {
      transform: none;
    }

    .learning-highlight {
      display: flex;
      align-items: center;
      gap: 24px;
      padding: 8px;

      .learning-icon {
        flex-shrink: 0;
        width: 80px;
        height: 80px;
        border-radius: 16px;
        background: linear-gradient(135deg, #4F46E5 0%, #7C3AED 100%);
        display: flex;
        align-items: center;
        justify-content: center;
        color: white;
        box-shadow: 0 8px 20px rgba(79, 70, 229, 0.3);
      }

      .learning-content {
        flex: 1;

        h2 {
          font-size: 20px;
          font-weight: 700;
          margin: 0 0 12px 0;
          color: var(--el-text-color-primary);
        }

        p {
          font-size: 14px;
          color: var(--el-text-color-regular);
          line-height: 1.6;
          margin: 0 0 16px 0;
        }

        .learning-features {
          display: flex;
          flex-wrap: wrap;
          gap: 8px;

          .feature-tag {
            padding: 6px 14px;
            background: linear-gradient(135deg, rgba(79, 70, 229, 0.1), rgba(124, 58, 237, 0.1));
            color: #4F46E5;
            border-radius: 20px;
            font-size: 13px;
            font-weight: 500;
            transition: all 0.2s ease;

            &:hover {
              background: linear-gradient(135deg, rgba(79, 70, 229, 0.2), rgba(124, 58, 237, 0.2));
              transform: translateY(-1px);
            }
          }
        }
      }

      .learning-action {
        flex-shrink: 0;
      }
    }
  }

  // 卡片标题样式
  .card-header-title {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 16px;
    font-weight: 600;
    color: var(--el-text-color-primary);

    .title-icon {
      font-size: 18px;
    }
  }

  // 快速操作卡片
  .quick-actions-card {
    &:hover {
      transform: none;
    }

    .quick-actions {
      display: grid;
      gap: 16px;

      .action-item {
        display: flex;
        align-items: center;
        gap: 16px;
        padding: 20px;
        border: 1px solid var(--el-border-color-lighter);
        border-radius: 12px;
        cursor: pointer;
        transition: all 0.3s ease;
        background: var(--el-bg-color);

        &:hover {
          border-color: var(--el-color-primary);
          background: linear-gradient(to right, rgba(79, 70, 229, 0.05), transparent);
          transform: translateX(4px);

          .action-arrow {
            transform: translateX(4px);
            color: var(--el-color-primary);
          }
        }

        .action-icon {
          width: 48px;
          height: 48px;
          border-radius: 12px;
          display: flex;
          align-items: center;
          justify-content: center;
          color: white;
          font-size: 22px;
          flex-shrink: 0;

          &.gradient-blue {
            background: linear-gradient(135deg, #3B82F6 0%, #60A5FA 100%);
            box-shadow: 0 4px 12px rgba(59, 130, 246, 0.3);
          }

          &.gradient-purple {
            background: linear-gradient(135deg, #8B5CF6 0%, #A78BFA 100%);
            box-shadow: 0 4px 12px rgba(139, 92, 246, 0.3);
          }

          &.gradient-green {
            background: linear-gradient(135deg, #10B981 0%, #34D399 100%);
            box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3);
          }

          &.gradient-orange {
            background: linear-gradient(135deg, #F59E0B 0%, #FBBF24 100%);
            box-shadow: 0 4px 12px rgba(245, 158, 11, 0.3);
          }
        }

        .action-content {
          flex: 1;

          h3 {
            margin: 0 0 4px 0;
            font-size: 16px;
            font-weight: 600;
            color: var(--el-text-color-primary);
          }

          p {
            margin: 0;
            font-size: 14px;
            color: var(--el-text-color-secondary);
          }
        }

        .action-arrow {
          color: var(--el-text-color-placeholder);
          transition: all 0.3s ease;
          font-size: 16px;
        }
      }
    }
  }

  // 最近分析卡片
  .recent-analyses-card {
    &:hover {
      transform: none;
    }

    .custom-table {
      .stock-code-cell {
        font-weight: 600;
        color: var(--el-color-primary);
      }

      .time-cell {
        color: var(--el-text-color-secondary);
        font-size: 13px;
      }

      .status-tag {
        font-weight: 500;
      }
    }

    .table-footer {
      text-align: center;
      margin-top: 16px;
      padding-top: 16px;
      border-top: 1px solid var(--el-border-color-lighter);
    }
  }

  // 市场快讯卡片
  .market-news-card {
    &:hover {
      transform: none;
    }

    .news-list {
      .news-item {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 14px 0;
        cursor: pointer;
        border-bottom: 1px solid var(--el-border-color-lighter);
        transition: all 0.2s ease;

        &:last-child {
          border-bottom: none;
        }

        &:hover {
          background: linear-gradient(to right, rgba(79, 70, 229, 0.05), transparent);
          margin: 0 -20px;
          padding: 14px 20px;
          border-radius: 8px;

          .news-arrow {
            opacity: 1;
            transform: translateX(0);
          }
        }

        .news-content {
          flex: 1;

          .news-title {
            font-size: 14px;
            color: var(--el-text-color-primary);
            margin-bottom: 6px;
            line-height: 1.5;
            font-weight: 500;
          }

          .news-meta {
            display: flex;
            align-items: center;
            gap: 12px;
            font-size: 12px;
            color: var(--el-text-color-placeholder);

            .news-source {
              padding: 2px 8px;
              background: var(--el-fill-color-light);
              border-radius: 4px;
            }
          }
        }

        .news-arrow {
          color: var(--el-text-color-placeholder);
          opacity: 0;
          transform: translateX(-10px);
          transition: all 0.2s ease;
        }
      }
    }
  }

  // 自选股卡片
  .favorites-card {
    &:hover {
      transform: none;
    }

    .card-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .empty-favorites {
      text-align: center;
      padding: 20px 0;
    }

    .favorites-list {
      .favorite-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 14px 0;
        border-bottom: 1px solid var(--el-border-color-lighter);
        cursor: pointer;
        transition: all 0.2s ease;

        &:hover {
          background: linear-gradient(to right, rgba(79, 70, 229, 0.05), transparent);
          margin: 0 -20px;
          padding: 14px 20px;
          border-radius: 8px;
        }

        &:last-child {
          border-bottom: none;
        }

        .stock-info {
          .stock-code {
            font-weight: 600;
            font-size: 14px;
            color: var(--el-color-primary);
          }

          .stock-name {
            font-size: 12px;
            color: var(--el-text-color-secondary);
            margin-top: 4px;
          }
        }

        .stock-price {
          text-align: right;

          .current-price {
            font-weight: 600;
            font-size: 15px;
            color: var(--el-text-color-primary);
          }

          .change-percent {
            font-size: 13px;
            margin-top: 4px;
            font-weight: 500;

            &.price-up {
              color: #EF4444;
            }

            &.price-down {
              color: #10B981;
            }

            &.price-neutral {
              color: var(--el-text-color-secondary);
            }
          }
        }
      }
    }

    .favorites-footer {
      text-align: center;
      padding-top: 12px;
      border-top: 1px solid var(--el-border-color-lighter);
      margin-top: 12px;
    }
  }

  // 模拟交易卡片
  .paper-trading-card {
    &:hover {
      transform: none;
    }

    .card-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .paper-account-info {
      display: flex;
      flex-direction: column;
      gap: 16px;

      .account-section {
        border: 1px solid var(--el-border-color-lighter);
        border-radius: 12px;
        padding: 16px;
        background: linear-gradient(to right, var(--el-fill-color-lighter), transparent);

        .account-section-title {
          font-size: 14px;
          font-weight: 600;
          color: var(--el-text-color-primary);
          margin-bottom: 12px;
          padding-bottom: 10px;
          border-bottom: 1px solid var(--el-border-color-lighter);
        }
      }

      .account-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 8px 0;

        .account-label {
          font-size: 13px;
          color: var(--el-text-color-secondary);
        }

        .account-value {
          font-size: 15px;
          font-weight: 600;
          color: var(--el-text-color-primary);

          &.primary {
            color: var(--el-color-primary);
            font-size: 17px;
          }

          &.price-up {
            color: #EF4444;
          }

          &.price-down {
            color: #10B981;
          }
        }
      }
    }

    .empty-state {
      text-align: center;
      padding: 24px 0;

      .empty-icon {
        font-size: 48px;
        color: var(--el-text-color-placeholder);
        margin-bottom: 12px;
      }

      p {
        color: var(--el-text-color-secondary);
        margin-bottom: 16px;
      }
    }
  }
}

// 响应式设计
@media (max-width: 768px) {
  .dashboard {
    .welcome-section {
      flex-direction: column;
      text-align: center;
      gap: 24px;
      padding: 32px 24px;

      .welcome-content {
        .welcome-title {
          font-size: 24px;
          flex-direction: column;
          gap: 12px;
        }

        .welcome-subtitle {
          font-size: 14px;
        }
      }

      .welcome-actions {
        justify-content: center;
        flex-wrap: wrap;
      }
    }

    .learning-highlight-card {
      .learning-highlight {
        flex-direction: column;
        text-align: center;

        .learning-content {
          .learning-features {
            justify-content: center;
          }
        }
      }
    }

    .main-content {
      .el-col {
        margin-bottom: 24px;
      }
    }
  }
}

// 暗色主题适配
html.dark {
  .dashboard {
    .welcome-section {
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4);
    }

    .learning-highlight-card {
      .learning-highlight {
        .learning-content {
          .learning-features {
            .feature-tag {
              background: linear-gradient(135deg, rgba(129, 140, 248, 0.15), rgba(167, 139, 250, 0.15));
              color: #A5B4FC;
            }
          }
        }
      }
    }

    .quick-actions-card {
      .action-item {
        &:hover {
          background: linear-gradient(to right, rgba(129, 140, 248, 0.1), transparent);
        }
      }
    }
  }
}
</style>
