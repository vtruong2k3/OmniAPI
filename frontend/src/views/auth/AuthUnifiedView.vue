<template>
  <div class="relative flex min-h-screen items-center justify-center overflow-x-hidden p-4 sm:p-6 lg:p-8">
    <!-- Ambient Cyber Background -->
    <div
      class="pointer-events-none absolute inset-0 bg-gradient-to-br from-slate-50 via-teal-50/20 to-emerald-50/30 dark:from-dark-950 dark:via-dark-900 dark:to-dark-950 transition-colors duration-500"
    ></div>

    <!-- Glowing Background Orbs -->
    <div class="pointer-events-none absolute inset-0 overflow-hidden">
      <div
        class="absolute -right-32 -top-32 h-96 w-96 rounded-full bg-primary-400/20 blur-3xl dark:bg-primary-500/15"
      ></div>
      <div
        class="absolute -bottom-32 -left-32 h-96 w-96 rounded-full bg-emerald-400/20 blur-3xl dark:bg-emerald-500/15"
      ></div>
      <div
        class="absolute left-1/2 top-1/2 h-[500px] w-[500px] -translate-x-1/2 -translate-y-1/2 rounded-full bg-teal-300/15 blur-3xl dark:bg-teal-500/10"
      ></div>

      <!-- Fine Grid Pattern -->
      <div
        class="absolute inset-0 bg-[linear-gradient(rgba(20,184,166,0.03)_1px,transparent_1px),linear-gradient(90deg,rgba(20,184,166,0.03)_1px,transparent_1px)] bg-[size:56px_56px]"
      ></div>
    </div>

    <!-- Top Navigation Actions (Home Link + Theme Toggle) -->
    <div class="absolute top-4 left-4 right-4 sm:top-6 sm:left-6 sm:right-6 z-20 flex items-center justify-between pointer-events-auto">
      <router-link
        to="/home"
        class="inline-flex items-center gap-2 rounded-xl border border-gray-200/80 bg-white/70 px-3.5 py-1.5 text-xs sm:text-sm font-medium text-gray-700 shadow-sm backdrop-blur-md transition-all hover:bg-white hover:text-primary-600 hover:shadow dark:border-dark-700/80 dark:bg-dark-900/70 dark:text-dark-200 dark:hover:bg-dark-800 dark:hover:text-primary-400"
      >
        <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
        </svg>
        <span>{{ t('common.backToHome', 'Trang chủ') }}</span>
      </router-link>

      <div class="flex items-center gap-2">
        <!-- Site Brand Pill (Mobile only, on desktop it's in the sliding panel) -->
        <div class="inline-flex items-center gap-2 rounded-xl border border-gray-200/70 bg-white/60 px-3 py-1.5 text-xs font-semibold text-gray-800 shadow-sm backdrop-blur-md dark:border-dark-700/70 dark:bg-dark-900/60 dark:text-dark-200 sm:hidden">
          <img :src="siteLogo || '/logo.svg'" alt="Logo" class="h-4 w-4 object-contain" />
          <span>{{ siteName }}</span>
        </div>

        <!-- Theme Toggle Button -->
        <button
          type="button"
          class="inline-flex h-9 w-9 items-center justify-center rounded-xl border border-gray-200/80 bg-white/70 text-gray-600 shadow-sm backdrop-blur-md transition-all hover:bg-white hover:text-primary-600 dark:border-dark-700/80 dark:bg-dark-900/70 dark:text-dark-300 dark:hover:bg-dark-800 dark:hover:text-primary-400"
          :title="isDark ? 'Chuyển sang chế độ sáng' : 'Chuyển sang chế độ tối'"
          @click="toggleTheme"
        >
          <!-- Moon Icon (when in dark mode) -->
          <svg v-if="isDark" class="h-4 w-4 text-amber-400" fill="currentColor" viewBox="0 0 20 20">
            <path d="M17.293 13.293A8 8 0 016.707 2.707a8.001 8.001 0 1010.586 10.586z" />
          </svg>
          <!-- Sun Icon (when in light mode) -->
          <svg v-else class="h-4 w-4 text-amber-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Master Content Container -->
    <div class="relative z-10 w-full max-w-5xl py-8 sm:py-12">
      <!-- Dual-Panel Sliding Container -->
      <AuthSlidingContainer
        v-model="isRegister"
        :welcome-back-title="t('auth.welcomeBackTitle', 'Chào Mừng Trở Lại!')"
        :welcome-back-subtitle="t('auth.welcomeBackDesc', 'Đã có tài khoản? Đăng nhập ngay để tiếp tục quản lý và phân phối API.')"
        :new-here-title="t('auth.newHereTitle', 'Xin Chào Bạn Mới!')"
        :new-here-subtitle="t('auth.newHereDesc', 'Đăng ký tài khoản mới để trải nghiệm hệ sinh thái AI Gateway tốc độ cao.')"
        :login-button-text="t('auth.signIn', 'Đăng Nhập')"
        :register-button-text="t('auth.createAccount', 'Đăng Ký')"
        :login-tab-label="t('auth.signIn', 'Đăng Nhập')"
        :register-tab-label="t('auth.createAccount', 'Đăng Ký')"
        @change="handleModeChange"
      >
        <!-- Custom Logo Slot inside Sliding Overlay -->
        <template #logo>
          <img :src="siteLogo || '/logo.svg'" alt="Logo" class="h-full w-full object-contain" />
        </template>

        <!-- Form Left: Sign In -->
        <template #login>
          <LoginView
            :is-embedded="true"
            @switch-mode="handleModeChange"
          />
        </template>

        <!-- Form Right: Sign Up -->
        <template #register>
          <RegisterView
            :is-embedded="true"
            @switch-mode="handleModeChange"
          />
        </template>
      </AuthSlidingContainer>

      <!-- Copyright Footer -->
      <div class="mt-8 text-center text-xs text-gray-400 dark:text-dark-500">
        &copy; {{ currentYear }} {{ siteName }}. All rights reserved.
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useI18n } from 'vue-i18n'
import { useAppStore } from '@/stores'
import { sanitizeUrl } from '@/utils/url'
import AuthSlidingContainer from '@/components/auth/AuthSlidingContainer.vue'
import LoginView from '@/views/auth/LoginView.vue'
import RegisterView from '@/views/auth/RegisterView.vue'

const { t } = useI18n()
const route = useRoute()
const router = useRouter()
const appStore = useAppStore()

// Initial state based on current route
const isRegister = ref(route.name === 'Register' || route.path.includes('/register'))

// Sync route changes (e.g. browser back/forward buttons)
watch(
  () => route.path,
  (newPath) => {
    isRegister.value = route.name === 'Register' || newPath.includes('/register')
  }
)

// Sync router URL whenever isRegister state changes
watch(isRegister, (newVal) => {
  const targetName = newVal ? 'Register' : 'Login'
  if (route.name !== targetName) {
    router.push({
      name: targetName,
      query: route.query
    })
  }
})

// Handle toggle click from sliding panel or embedded buttons
function handleModeChange(mode: 'login' | 'register') {
  isRegister.value = mode === 'register'
}

// Theme state
const isDark = ref(false)

function toggleTheme() {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
}

function initTheme() {
  const savedTheme = localStorage.getItem('theme')
  if (
    savedTheme === 'dark' ||
    (!savedTheme && window.matchMedia('(prefers-color-scheme: dark)').matches)
  ) {
    isDark.value = true
    document.documentElement.classList.add('dark')
  } else {
    isDark.value = false
    document.documentElement.classList.remove('dark')
  }
}

// Brand metadata
const siteName = computed(() => appStore.siteName || 'OmniAPI')
const siteLogo = computed(() => sanitizeUrl(appStore.siteLogo || '', { allowRelative: true, allowDataUrl: true }))
const currentYear = computed(() => new Date().getFullYear())

onMounted(() => {
  initTheme()
  appStore.fetchPublicSettings()
})
</script>

<style scoped>
/* Page transition smoothness */
</style>
