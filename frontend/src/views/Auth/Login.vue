<template>
  <div class="login-page">
    <!-- 动态背景 -->
    <div class="background-animation">
      <div class="gradient-orb orb-1"></div>
      <div class="gradient-orb orb-2"></div>
      <div class="gradient-orb orb-3"></div>
    </div>

    <div class="login-container">
      <!-- Logo 和标题 -->
      <div class="login-header">
        <div class="logo-wrapper">
          <img src="/logo.svg" alt="TradingAgents-CN" class="logo" />
          <div class="logo-glow"></div>
        </div>
        <h1 class="title">TradingAgents-CN</h1>
        <p class="subtitle">多智能体股票分析学习平台</p>
      </div>

      <!-- 登录卡片 -->
      <div class="login-card glass-effect">
        <el-form
          :model="loginForm"
          :rules="loginRules"
          ref="loginFormRef"
          label-position="top"
          size="large"
          class="login-form"
        >
          <el-form-item label="用户名" prop="username">
            <el-input
              v-model="loginForm.username"
              placeholder="请输入用户名"
              prefix-icon="User"
              class="custom-input"
            />
          </el-form-item>

          <el-form-item label="密码" prop="password">
            <el-input
              v-model="loginForm.password"
              type="password"
              placeholder="请输入密码"
              prefix-icon="Lock"
              show-password
              class="custom-input"
              @keyup.enter="handleLogin"
            />
          </el-form-item>

          <el-form-item>
            <div class="form-options">
              <el-checkbox v-model="loginForm.rememberMe">
                记住我
              </el-checkbox>
            </div>
          </el-form-item>

          <el-form-item>
            <el-button
              type="primary"
              size="large"
              class="login-button"
              :loading="loginLoading"
              @click="handleLogin"
            >
              <span v-if="!loginLoading">登 录</span>
              <span v-else>登录中...</span>
            </el-button>
          </el-form-item>

          <el-form-item>
            <div class="login-tip">
              <el-icon><InfoFilled /></el-icon>
              <span>开源版默认账号：admin / admin123</span>
            </div>
          </el-form-item>
        </el-form>
      </div>

      <!-- 页脚 -->
      <div class="login-footer">
        <p class="copyright">&copy; 2025 TradingAgents-CN. All rights reserved.</p>
        <p class="disclaimer">
          TradingAgents-CN 是一个 AI 多 Agents 的股票分析学习平台。平台中的分析结论、观点和"投资建议"均由 AI 自动生成，仅用于学习、研究与交流，不构成任何形式的投资建议或承诺。
        </p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { InfoFilled } from '@element-plus/icons-vue'
import { useAuthStore } from '@/stores/auth'

const router = useRouter()
const authStore = useAuthStore()

const loginFormRef = ref()
const loginLoading = ref(false)

const loginForm = reactive({
  username: '',
  password: '',
  rememberMe: false
})

const loginRules = {
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, message: '密码长度不能少于6位', trigger: 'blur' }
  ]
}

const handleLogin = async () => {
  if (loginLoading.value) {
    return
  }

  try {
    await loginFormRef.value.validate()

    loginLoading.value = true

    const success = await authStore.login({
      username: loginForm.username,
      password: loginForm.password
    })

    if (success) {
      ElMessage.success('登录成功')
      const redirectPath = authStore.getAndClearRedirectPath()
      router.push(redirectPath)
    } else {
      ElMessage.error('用户名或密码错误')
    }

  } catch (error: any) {
    console.error('登录失败:', error)
    if (error.message && !error.message.includes('validate')) {
      ElMessage.error('登录失败，请重试')
    }
  } finally {
    loginLoading.value = false
  }
}
</script>

<style lang="scss" scoped>
.login-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  position: relative;
  overflow: hidden;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
}

// 动态背景动画
.background-animation {
  position: absolute;
  inset: 0;
  overflow: hidden;
  z-index: 0;
}

.gradient-orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  opacity: 0.5;
  animation: float 20s ease-in-out infinite;

  &.orb-1 {
    width: 600px;
    height: 600px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    top: -200px;
    left: -100px;
    animation-delay: 0s;
  }

  &.orb-2 {
    width: 500px;
    height: 500px;
    background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
    bottom: -150px;
    right: -100px;
    animation-delay: -7s;
  }

  &.orb-3 {
    width: 400px;
    height: 400px;
    background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    animation-delay: -14s;
  }
}

@keyframes float {
  0%, 100% {
    transform: translate(0, 0) scale(1);
  }
  25% {
    transform: translate(30px, -30px) scale(1.05);
  }
  50% {
    transform: translate(-20px, 20px) scale(0.95);
  }
  75% {
    transform: translate(-30px, -20px) scale(1.02);
  }
}

.login-container {
  width: 100%;
  max-width: 420px;
  position: relative;
  z-index: 1;
}

.login-header {
  text-align: center;
  margin-bottom: 32px;
  color: white;

  .logo-wrapper {
    position: relative;
    display: inline-block;
    margin-bottom: 20px;

    .logo {
      width: 80px;
      height: 80px;
      position: relative;
      z-index: 1;
      filter: drop-shadow(0 4px 20px rgba(102, 126, 234, 0.5));
      animation: pulse-glow 3s ease-in-out infinite;
    }

    .logo-glow {
      position: absolute;
      inset: -10px;
      background: radial-gradient(circle, rgba(102, 126, 234, 0.4) 0%, transparent 70%);
      border-radius: 50%;
      animation: glow-pulse 3s ease-in-out infinite;
    }
  }

  .title {
    font-size: 32px;
    font-weight: 700;
    margin: 0 0 8px 0;
    background: linear-gradient(135deg, #ffffff 0%, #e0e7ff 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    text-shadow: 0 2px 20px rgba(255, 255, 255, 0.2);
  }

  .subtitle {
    font-size: 16px;
    opacity: 0.85;
    margin: 0;
    letter-spacing: 1px;
  }
}

@keyframes pulse-glow {
  0%, 100% {
    filter: drop-shadow(0 4px 20px rgba(102, 126, 234, 0.5));
  }
  50% {
    filter: drop-shadow(0 4px 30px rgba(102, 126, 234, 0.8));
  }
}

@keyframes glow-pulse {
  0%, 100% {
    opacity: 0.5;
    transform: scale(1);
  }
  50% {
    opacity: 0.8;
    transform: scale(1.1);
  }
}

.login-card {
  padding: 40px;
  border-radius: 24px;
  animation: slide-up 0.6s ease-out;
}

.glass-effect {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow: 
    0 8px 32px rgba(0, 0, 0, 0.2),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
}

@keyframes slide-up {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.login-form {
  :deep(.el-form-item__label) {
    color: rgba(255, 255, 255, 0.9) !important;
    font-weight: 500;
    font-size: 14px;
  }

  :deep(.el-input__wrapper) {
    background: rgba(255, 255, 255, 0.08) !important;
    border: 1px solid rgba(255, 255, 255, 0.15) !important;
    box-shadow: none !important;
    border-radius: 12px !important;
    padding: 4px 16px !important;
    transition: all 0.3s ease !important;

    &:hover {
      border-color: rgba(255, 255, 255, 0.3) !important;
      background: rgba(255, 255, 255, 0.12) !important;
    }

    &.is-focus {
      border-color: #818CF8 !important;
      background: rgba(255, 255, 255, 0.15) !important;
      box-shadow: 0 0 0 3px rgba(129, 140, 248, 0.2) !important;
    }

    .el-input__inner {
      color: white !important;

      &::placeholder {
        color: rgba(255, 255, 255, 0.5) !important;
      }
    }

    .el-input__prefix {
      color: rgba(255, 255, 255, 0.6) !important;
    }
  }

  :deep(.el-checkbox__label) {
    color: rgba(255, 255, 255, 0.8) !important;
  }

  :deep(.el-checkbox__inner) {
    background: rgba(255, 255, 255, 0.1);
    border-color: rgba(255, 255, 255, 0.3);
  }

  :deep(.el-checkbox__input.is-checked .el-checkbox__inner) {
    background: linear-gradient(135deg, #4F46E5 0%, #6366F1 100%);
    border-color: #4F46E5;
  }
}

.form-options {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
}

.login-button {
  width: 100%;
  height: 48px !important;
  border-radius: 12px !important;
  font-size: 16px !important;
  font-weight: 600 !important;
  background: linear-gradient(135deg, #4F46E5 0%, #7C3AED 100%) !important;
  border: none !important;
  box-shadow: 0 4px 20px rgba(79, 70, 229, 0.4) !important;
  transition: all 0.3s ease !important;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 30px rgba(79, 70, 229, 0.5) !important;
    background: linear-gradient(135deg, #5B52F0 0%, #8B5CF6 100%) !important;
  }

  &:active {
    transform: translateY(0);
  }
}

.login-tip {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  width: 100%;
  padding: 12px 16px;
  background: rgba(79, 70, 229, 0.15);
  border-radius: 10px;
  color: rgba(255, 255, 255, 0.85);
  font-size: 13px;

  .el-icon {
    color: #818CF8;
  }
}

.login-footer {
  text-align: center;
  margin-top: 32px;
  color: rgba(255, 255, 255, 0.7);

  .copyright {
    margin: 0;
    font-size: 14px;
  }

  .disclaimer {
    margin-top: 12px;
    font-size: 12px;
    line-height: 1.6;
    max-width: 100%;
    opacity: 0.7;
  }
}

// 响应式适配
@media (max-width: 480px) {
  .login-card {
    padding: 32px 24px;
  }

  .login-header {
    .title {
      font-size: 26px;
    }

    .subtitle {
      font-size: 14px;
    }
  }
}
</style>
