<template>
  <div class="register-page">
    <!-- 动态背景 -->
    <div class="background-animation">
      <div class="gradient-orb orb-1"></div>
      <div class="gradient-orb orb-2"></div>
      <div class="gradient-orb orb-3"></div>
    </div>

    <div class="register-container">
      <!-- Logo 和标题 -->
      <div class="register-header">
        <div class="logo-wrapper">
          <img src="/logo.svg" alt="TradingAgents-CN" class="logo" />
          <div class="logo-glow"></div>
        </div>
        <h1 class="title">TradingAgents-CN</h1>
        <p class="subtitle">创建您的账号</p>
      </div>

      <!-- 注册卡片 -->
      <div class="register-card glass-effect">
        <el-form
          :model="registerForm"
          :rules="registerRules"
          ref="registerFormRef"
          label-position="top"
          size="large"
          class="register-form"
        >
          <el-form-item label="用户名" prop="username">
            <el-input
              v-model="registerForm.username"
              placeholder="请输入用户名（3-20个字符）"
              prefix-icon="User"
              class="custom-input"
            />
          </el-form-item>

          <el-form-item label="邮箱" prop="email">
            <el-input
              v-model="registerForm.email"
              placeholder="请输入邮箱地址"
              prefix-icon="Message"
              class="custom-input"
            />
          </el-form-item>

          <el-form-item label="密码" prop="password">
            <el-input
              v-model="registerForm.password"
              type="password"
              placeholder="请输入密码（至少6位）"
              prefix-icon="Lock"
              show-password
              class="custom-input"
            />
          </el-form-item>

          <el-form-item label="确认密码" prop="confirmPassword">
            <el-input
              v-model="registerForm.confirmPassword"
              type="password"
              placeholder="请再次输入密码"
              prefix-icon="Lock"
              show-password
              class="custom-input"
              @keyup.enter="handleRegister"
            />
          </el-form-item>

          <el-form-item>
            <el-button
              type="primary"
              size="large"
              class="register-button"
              :loading="registerLoading"
              @click="handleRegister"
            >
              <span v-if="!registerLoading">注 册</span>
              <span v-else>注册中...</span>
            </el-button>
          </el-form-item>

          <el-form-item>
            <div class="login-link">
              <span>已有账号？</span>
              <el-button type="primary" link @click="goToLogin">立即登录</el-button>
            </div>
          </el-form-item>
        </el-form>
      </div>

      <!-- 页脚 -->
      <div class="register-footer">
        <p class="copyright">&copy; 2025 TradingAgents-CN. All rights reserved.</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import request from '@/api/request'

const router = useRouter()

const registerFormRef = ref()
const registerLoading = ref(false)

const registerForm = reactive({
  username: '',
  email: '',
  password: '',
  confirmPassword: ''
})

const validateConfirmPassword = (rule: any, value: string, callback: any) => {
  if (value !== registerForm.password) {
    callback(new Error('两次输入的密码不一致'))
  } else {
    callback()
  }
}

const registerRules = {
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' },
    { min: 3, max: 20, message: '用户名长度为3-20个字符', trigger: 'blur' }
  ],
  email: [
    { required: true, message: '请输入邮箱', trigger: 'blur' },
    { type: 'email', message: '请输入正确的邮箱格式', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, message: '密码长度不能少于6位', trigger: 'blur' }
  ],
  confirmPassword: [
    { required: true, message: '请确认密码', trigger: 'blur' },
    { validator: validateConfirmPassword, trigger: 'blur' }
  ]
}

const handleRegister = async () => {
  if (registerLoading.value) {
    return
  }

  try {
    await registerFormRef.value.validate()

    registerLoading.value = true

    const response = await request.post('/auth/register', {
      username: registerForm.username,
      email: registerForm.email,
      password: registerForm.password
    })

    if (response.data?.success) {
      ElMessage.success('注册成功，请登录')
      router.push('/login')
    } else {
      ElMessage.error(response.data?.message || '注册失败')
    }

  } catch (error: any) {
    console.error('注册失败:', error)
    if (error.response?.data?.detail) {
      ElMessage.error(error.response.data.detail)
    } else if (error.message && !error.message.includes('validate')) {
      ElMessage.error('注册失败，请重试')
    }
  } finally {
    registerLoading.value = false
  }
}

const goToLogin = () => {
  router.push('/login')
}
</script>

<style lang="scss" scoped>
.register-page {
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
  opacity: 0.6;
  animation: float 20s ease-in-out infinite;

  &.orb-1 {
    width: 500px;
    height: 500px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    top: -200px;
    left: -100px;
    animation-delay: 0s;
  }

  &.orb-2 {
    width: 400px;
    height: 400px;
    background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
    bottom: -150px;
    right: -100px;
    animation-delay: -5s;
  }

  &.orb-3 {
    width: 300px;
    height: 300px;
    background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    animation-delay: -10s;
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

.register-container {
  width: 100%;
  max-width: 440px;
  position: relative;
  z-index: 1;
}

.register-header {
  text-align: center;
  margin-bottom: 32px;
  color: white;

  .logo-wrapper {
    position: relative;
    display: inline-block;
    margin-bottom: 20px;
  }

  .logo {
    width: 72px;
    height: 72px;
    animation: pulse-glow 3s ease-in-out infinite;
  }

  .logo-glow {
    position: absolute;
    inset: -10px;
    background: radial-gradient(circle, rgba(102, 126, 234, 0.4) 0%, transparent 70%);
    border-radius: 50%;
    animation: glow-pulse 3s ease-in-out infinite;
    z-index: -1;
  }

  .title {
    font-size: 32px;
    font-weight: 700;
    margin: 0 0 8px 0;
    background: linear-gradient(135deg, #fff 0%, #e0e7ff 100%);
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

.register-card {
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

.register-form {
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
  }

  :deep(.el-input__inner) {
    color: white !important;
    height: 44px !important;

    &::placeholder {
      color: rgba(255, 255, 255, 0.5) !important;
    }
  }

  :deep(.el-input__prefix) {
    color: rgba(255, 255, 255, 0.6) !important;
  }

  :deep(.el-input__suffix) {
    color: rgba(255, 255, 255, 0.6) !important;
  }
}

.register-button {
  width: 100% !important;
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

.login-link {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  width: 100%;
  color: rgba(255, 255, 255, 0.7);
  font-size: 14px;

  :deep(.el-button) {
    font-size: 14px;
    color: #818CF8 !important;
    
    &:hover {
      color: #A5B4FC !important;
    }
  }
}

.register-footer {
  text-align: center;
  margin-top: 32px;
  color: rgba(255, 255, 255, 0.7);

  .copyright {
    margin: 0;
    font-size: 14px;
  }
}

// 响应式适配
@media (max-width: 480px) {
  .register-card {
    padding: 32px 24px;
  }

  .register-header {
    .title {
      font-size: 26px;
    }

    .subtitle {
      font-size: 14px;
    }
  }
}
</style>
