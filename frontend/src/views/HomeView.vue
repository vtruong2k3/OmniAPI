<template>
  <!-- Custom Home Content: Full Page Mode -->
  <div v-if="hasHomeContent" class="min-h-screen">
    <!-- iframe mode -->
    <iframe
      v-if="isHomeContentUrl"
      :src="homeContent.trim()"
      class="h-screen w-full border-0"
      allowfullscreen
    ></iframe>
    <!-- HTML mode - SECURITY: homeContent is admin-only setting, XSS risk is acceptable -->
    <div v-else v-html="homeContent"></div>
  </div>

  <!-- Compact Home Page -->
  <div
    v-else-if="compactHomeEnabled"
    data-testid="compact-home"
    class="flex min-h-screen flex-col bg-gray-50 text-gray-900 dark:bg-dark-950 dark:text-white"
  >
    <header class="border-b border-gray-200 px-4 py-4 sm:px-6 dark:border-dark-800">
      <nav class="mx-auto flex max-w-5xl flex-wrap items-center justify-between gap-3 sm:gap-4">
        <div class="flex min-w-0 flex-1 items-center gap-3">
          <img
            :src="siteLogo || '/logo.svg'"
            alt="Logo"
            class="h-9 w-9 shrink-0 rounded-lg object-contain"
          />
          <span class="min-w-0 truncate text-base font-semibold">{{ siteName }}</span>
        </div>
        <div class="flex max-w-full shrink-0 flex-wrap items-center justify-end gap-2">
          <LocaleSwitcher />
          <a
            v-if="docUrl"
            :href="docUrl"
            target="_blank"
            rel="noopener noreferrer"
            class="flex h-10 w-10 shrink-0 items-center justify-center rounded-lg text-gray-500 hover:bg-gray-100 dark:text-dark-400 dark:hover:bg-dark-800"
            :title="t('home.viewDocs')"
          >
            <Icon name="book" size="md" />
          </a>
          <router-link
            v-if="showModelPlazaEntry"
            to="/model-plaza"
            class="flex h-10 shrink-0 items-center gap-1.5 rounded-lg px-2.5 text-sm font-medium text-gray-500 hover:bg-gray-100 hover:text-gray-700 dark:text-dark-400 dark:hover:bg-dark-800 dark:hover:text-white"
            :title="t('nav.modelPlaza')"
          >
            <Icon name="grid" size="md" />
            <span class="hidden sm:inline">{{ t('nav.modelPlaza') }}</span>
          </router-link>
          <button
            class="flex h-10 w-10 shrink-0 items-center justify-center rounded-lg text-gray-500 hover:bg-gray-100 dark:text-dark-400 dark:hover:bg-dark-800"
            :title="isDark ? t('home.switchToLight') : t('home.switchToDark')"
            @click="toggleTheme"
          >
            <Icon v-if="isDark" name="sun" size="md" />
            <Icon v-else name="moon" size="md" />
          </button>
          <router-link
            :to="isAuthenticated ? dashboardPath : '/login'"
            class="inline-flex min-h-10 shrink-0 items-center justify-center rounded-lg bg-gray-900 px-4 py-2 text-sm font-medium text-white hover:bg-gray-800 dark:bg-white dark:text-gray-900 dark:hover:bg-gray-200"
          >
            {{ isAuthenticated ? t('home.dashboard') : t('home.login') }}
          </router-link>
        </div>
      </nav>
    </header>

    <main class="flex min-w-0 flex-1 items-center justify-center px-4 py-16 sm:px-6">
      <div class="min-w-0 max-w-2xl text-center">
        <img
          :src="siteLogo || '/logo.svg'"
          alt="Logo"
          class="mx-auto mb-6 h-20 w-20 rounded-2xl object-contain"
        />
        <h1 class="[overflow-wrap:anywhere] text-3xl font-bold md:text-4xl">{{ siteName }}</h1>
        <p class="mt-4 whitespace-pre-wrap [overflow-wrap:anywhere] text-base text-gray-600 dark:text-dark-300">{{ siteSubtitle }}</p>
        <router-link
          :to="isAuthenticated ? dashboardPath : '/login'"
          class="mt-8 inline-flex min-h-10 items-center justify-center rounded-lg bg-primary-600 px-5 py-2.5 text-sm font-medium text-white hover:bg-primary-700"
        >
          {{ isAuthenticated ? t('home.goToDashboard') : t('home.login') }}
        </router-link>
      </div>
    </main>

    <footer class="min-w-0 border-t border-gray-200 px-4 py-5 text-center text-sm text-gray-500 [overflow-wrap:anywhere] sm:px-6 dark:border-dark-800 dark:text-dark-400">
      &copy; {{ currentYear }} {{ siteName }}
    </footer>
  </div>

  <!-- Default Home Page -->
  <div
    v-else
    class="relative flex min-h-screen flex-col overflow-hidden bg-gradient-to-br from-gray-50 via-primary-50/20 to-gray-100 dark:from-dark-950 dark:via-dark-900 dark:to-dark-950"
  >
    <!-- Background Aura Glows & Technical Grid -->
    <div class="pointer-events-none absolute inset-0 overflow-hidden">
      <div
        class="absolute -right-40 -top-40 h-[500px] w-[500px] rounded-full bg-cyan-500/15 blur-[120px] dark:bg-cyan-500/20"
      ></div>
      <div
        class="absolute -bottom-40 -left-40 h-[500px] w-[500px] rounded-full bg-violet-600/15 blur-[140px] dark:bg-violet-600/20"
      ></div>
      <div
        class="absolute left-1/3 top-1/4 h-80 w-80 rounded-full bg-emerald-500/10 blur-[100px] dark:bg-emerald-500/15"
      ></div>
      <div
        class="absolute inset-0 bg-[linear-gradient(rgba(6,182,212,0.03)_1px,transparent_1px),linear-gradient(90deg,rgba(6,182,212,0.03)_1px,transparent_1px)] bg-[size:48px_48px]"
      ></div>
    </div>

    <!-- Modern Glass Header -->
    <header class="relative z-20 border-b border-white/10 px-4 py-3.5 backdrop-blur-xl sm:px-6">
      <nav class="mx-auto flex max-w-7xl items-center justify-between gap-4">
        <!-- Left: Logo & Live Status -->
        <div class="flex items-center gap-4">
          <router-link to="/" class="flex items-center gap-2.5">
            <div class="flex h-9 w-9 items-center justify-center overflow-hidden rounded-xl border border-white/20 bg-white/80 p-1 shadow-md shadow-cyan-500/10 backdrop-blur-md dark:bg-dark-900/80">
              <img :src="siteLogo || '/logo.svg'" alt="Logo" class="h-full w-full object-contain" />
            </div>
            <span class="text-base font-bold tracking-tight text-gray-900 dark:text-white sm:text-lg">{{ siteName }}</span>
          </router-link>

          <!-- Live Network Operational Badge -->
          <div class="hidden items-center gap-2 rounded-full border border-emerald-500/20 bg-emerald-500/10 px-3 py-1 font-mono text-[11px] font-medium text-emerald-400 sm:flex">
            <span class="relative flex h-2 w-2">
              <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-emerald-400 opacity-75"></span>
              <span class="relative inline-flex h-2 w-2 rounded-full bg-emerald-500"></span>
            </span>
            <span>All 50+ Models Online</span>
          </div>
        </div>

        <!-- Right: Nav Actions -->
        <div class="flex items-center gap-2 sm:gap-3">
          <!-- Language Switcher -->
          <LocaleSwitcher />

          <!-- Doc Link -->
          <a
            v-if="docUrl"
            :href="docUrl"
            target="_blank"
            rel="noopener noreferrer"
            class="rounded-xl p-2 text-gray-500 transition-colors hover:bg-white/60 hover:text-gray-700 dark:text-dark-400 dark:hover:bg-dark-800 dark:hover:text-white"
            :title="t('home.viewDocs')"
          >
            <Icon name="book" size="md" />
          </a>

          <!-- Model Plaza Link -->
          <router-link
            v-if="showModelPlazaEntry"
            to="/model-plaza"
            class="inline-flex items-center gap-1.5 rounded-xl px-3 py-2 text-xs font-medium text-gray-600 transition-all hover:bg-white/60 hover:text-gray-900 dark:text-dark-300 dark:hover:bg-dark-800 dark:hover:text-white"
            :title="t('nav.modelPlaza')"
          >
            <Icon name="grid" size="sm" />
            <span class="hidden sm:inline">{{ t('nav.modelPlaza') }}</span>
          </router-link>

          <!-- Theme Toggle -->
          <button
            id="theme-toggle-btn"
            @click="toggleTheme"
            class="rounded-xl p-2 text-gray-500 transition-colors hover:bg-white/60 hover:text-gray-700 dark:text-dark-400 dark:hover:bg-dark-800 dark:hover:text-white"
            :title="isDark ? t('home.switchToLight') : t('home.switchToDark')"
          >
            <Icon v-if="isDark" name="sun" size="md" />
            <Icon v-else name="moon" size="md" />
          </button>

          <!-- Login / Dashboard Button -->
          <router-link
            v-if="isAuthenticated"
            :to="dashboardPath"
            class="inline-flex items-center gap-1.5 rounded-xl border border-cyan-500/30 bg-gradient-to-r from-cyan-500/20 to-primary-500/20 px-4 py-2 text-xs font-semibold text-cyan-300 shadow-sm transition-all hover:border-cyan-500/50 hover:bg-cyan-500/30"
          >
            <span class="flex h-4 w-4 items-center justify-center rounded-full bg-cyan-500 text-[10px] text-white">
              {{ userInitial }}
            </span>
            <span>{{ t('home.dashboard') }}</span>
            <svg class="h-3 w-3" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
            </svg>
          </router-link>
          <router-link
            v-else
            to="/login"
            class="inline-flex items-center rounded-xl bg-gradient-to-r from-cyan-500 via-primary-500 to-emerald-500 px-4 py-2 text-xs font-semibold text-white shadow-md shadow-cyan-500/20 transition-all hover:scale-105 active:scale-95"
          >
            {{ t('home.login') }}
          </router-link>
        </div>
      </nav>
    </header>

    <!-- Main Content -->
    <main class="relative z-10 flex-1 px-4 py-12 sm:px-6 lg:px-8">
      <div class="mx-auto max-w-7xl">
        <!-- Hero Section - 2 Columns (Text Left, 3D Right) -->
        <div class="mb-12 flex flex-col items-center justify-between gap-8 lg:flex-row lg:gap-12">
          <!-- Left Column: Content & Actions -->
          <div class="flex-1 text-center lg:text-left">
            <!-- Subtitle Badge -->
            <div class="mb-5 inline-flex items-center gap-2 rounded-full border border-cyan-500/30 bg-cyan-500/10 px-4 py-1.5 shadow-sm backdrop-blur-md">
              <span class="flex h-2 w-2 animate-pulse rounded-full bg-cyan-400"></span>
              <span class="font-mono text-xs font-semibold text-cyan-300">⚡ OmniAPI 2.0 &bull; Intelligent Model &amp; Quota Router</span>
            </div>

            <!-- Main Headline -->
            <h1 class="mb-5 text-4xl font-extrabold tracking-tight text-gray-900 dark:text-white sm:text-5xl lg:text-6xl">
              Một API Key Duy Nhất.<br />
              <span class="text-gradient-aurora">Truy Cập Toàn Bộ Siêu AI.</span>
            </h1>

            <!-- Subtitle -->
            <p class="mb-8 max-w-xl text-base text-gray-600 dark:text-dark-300 sm:text-lg">
              Hợp nhất hạn ngạch từ các gói đăng ký Claude Pro/Team, OpenAI Codex, Gemini, Grok. Tự động duy trì phiên (Sticky Session), cân bằng tải thông minh và giảm tới 80% chi phí Prompt Cache.
            </p>

            <!-- CTA Buttons -->
            <div class="mb-10 flex flex-wrap items-center justify-center gap-4 lg:justify-start">
              <router-link
                :to="isAuthenticated ? dashboardPath : '/login'"
                class="inline-flex items-center gap-2 rounded-xl bg-gradient-to-r from-cyan-500 via-primary-500 to-emerald-500 px-7 py-3.5 text-base font-semibold text-white shadow-lg shadow-cyan-500/25 transition-all hover:scale-105 hover:shadow-cyan-500/40 active:scale-95"
              >
                <span>{{ isAuthenticated ? t('home.goToDashboard') : t('home.getStarted') }}</span>
                <Icon name="arrowRight" size="md" :stroke-width="2.5" />
              </router-link>

              <router-link
                v-if="showModelPlazaEntry"
                to="/model-plaza"
                class="glass-card inline-flex items-center gap-2 rounded-xl px-6 py-3.5 text-base font-medium text-gray-700 transition-all hover:bg-white/90 hover:text-gray-900 dark:text-dark-200 dark:hover:bg-dark-800 dark:hover:text-white"
              >
                <Icon name="grid" size="md" />
                <span>{{ t('nav.modelPlaza') }}</span>
              </router-link>
            </div>

            <!-- Metric Stats Bar (4 Highlights) -->
            <div class="grid grid-cols-2 gap-3 sm:grid-cols-4 sm:gap-4">
              <div class="glass-card rounded-xl p-3 text-center transition-all hover:border-cyan-500/40">
                <div class="font-mono text-xl font-bold text-cyan-400">&lt; 30ms</div>
                <div class="text-[11px] text-gray-500 dark:text-dark-400">Failover Latency</div>
              </div>
              <div class="glass-card rounded-xl p-3 text-center transition-all hover:border-emerald-500/40">
                <div class="font-mono text-xl font-bold text-emerald-400">50+</div>
                <div class="text-[11px] text-gray-500 dark:text-dark-400">AI Models</div>
              </div>
              <div class="glass-card rounded-xl p-3 text-center transition-all hover:border-blue-500/40">
                <div class="font-mono text-xl font-bold text-blue-400">99.99%</div>
                <div class="text-[11px] text-gray-500 dark:text-dark-400">Uptime SLA</div>
              </div>
              <div class="glass-card rounded-xl p-3 text-center transition-all hover:border-orange-500/40">
                <div class="font-mono text-xl font-bold text-orange-400">100K+</div>
                <div class="text-[11px] text-gray-500 dark:text-dark-400">RPS Concurrency</div>
              </div>
            </div>
          </div>

          <!-- Right Column: 3D Quantum Gateway Core Component -->
          <div class="w-full flex-1 lg:max-w-xl">
            <AiGatewayGlobe3D />
          </div>
        </div>

        <!-- Supported AI Models & Providers Logo Ecosystem Showcase -->
        <ModelLogoCloud class="mb-20" />

        <!-- Bento Grid 2.0 Core Features -->
        <BentoFeatures class="mb-20" />

        <!-- Model Matrix 2025-2026 Showcase -->
        <ModelMatrix :show-model-plaza-entry="showModelPlazaEntry" class="mb-20" />

        <!-- Quick Onboarding: 3 Steps & Cyber Animated CTA -->
        <QuickOnboarding
          :api-base-url="apiBaseUrl"
          :is-authenticated="isAuthenticated"
          :dashboard-path="dashboardPath"
          class="mb-20"
        />
      </div>
    </main>

    <!-- Modern 4-Column SaaS Footer -->
    <footer class="relative z-10 border-t border-gray-200/80 bg-gray-50/95 px-6 py-14 backdrop-blur-2xl dark:border-white/10 dark:bg-dark-950/90">
      <div class="mx-auto max-w-7xl">
        <div class="grid grid-cols-1 gap-10 sm:grid-cols-2 lg:grid-cols-5">
          <!-- Col 1: Brand (Span 2) -->
          <div class="lg:col-span-2">
            <div class="flex items-center gap-2.5">
              <div class="flex h-9 w-9 items-center justify-center overflow-hidden rounded-xl border border-gray-200 bg-white p-1 shadow-md shadow-cyan-500/10 backdrop-blur-md dark:border-white/20 dark:bg-dark-900/80">
                <img :src="siteLogo || '/logo.svg'" alt="Logo" class="h-full w-full object-contain" />
              </div>
              <span class="text-lg font-bold tracking-tight text-gray-900 dark:text-white">{{ siteName }}</span>
            </div>
            <p class="mt-3 max-w-sm text-sm leading-relaxed text-gray-600 dark:text-dark-300">
              Nền tảng AI API Gateway thông minh, hợp nhất hạn ngạch từ các gói đăng ký siêu mô hình AI hàng đầu thế giới với độ ổn định 99.99%.
            </p>
            <!-- Operational Live Badge -->
            <div class="mt-5 inline-flex items-center gap-2 rounded-full border border-emerald-500/30 bg-emerald-500/10 px-3 py-1 font-mono text-xs text-emerald-600 dark:text-emerald-400">
              <span class="relative flex h-2 w-2">
                <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-emerald-400 opacity-75"></span>
                <span class="relative inline-flex h-2 w-2 rounded-full bg-emerald-500"></span>
              </span>
              <span>All 50+ Models Operational &bull; 99.99% Uptime</span>
            </div>
          </div>

          <!-- Col 2: Product -->
          <div>
            <h4 class="mb-4 font-mono text-xs font-semibold tracking-wider uppercase text-gray-500 dark:text-gray-400">
              Sản Phẩm
            </h4>
            <ul class="space-y-2.5 text-sm text-gray-600 dark:text-dark-300">
              <li>
                <router-link v-if="showModelPlazaEntry" to="/model-plaza" class="transition-colors hover:text-cyan-600 dark:hover:text-cyan-400">
                  {{ t('nav.modelPlaza') }}
                </router-link>
                <span v-else class="text-gray-400">50+ AI Models</span>
              </li>
              <li>
                <router-link :to="isAuthenticated ? dashboardPath : '/login'" class="transition-colors hover:text-cyan-600 dark:hover:text-cyan-400">
                  Gateway Router
                </router-link>
              </li>
              <li>
                <router-link :to="isAuthenticated ? dashboardPath : '/login'" class="transition-colors hover:text-cyan-600 dark:hover:text-cyan-400">
                  Sticky Session &amp; Cache
                </router-link>
              </li>
              <li>
                <router-link :to="isAuthenticated ? dashboardPath : '/login'" class="transition-colors hover:text-cyan-600 dark:hover:text-cyan-400">
                  Hạch Toán Token Minh Bạch
                </router-link>
              </li>
            </ul>
          </div>

          <!-- Col 3: Developers -->
          <div>
            <h4 class="mb-4 font-mono text-xs font-semibold tracking-wider uppercase text-gray-500 dark:text-gray-400">
              Lập Trình Viên
            </h4>
            <ul class="space-y-2.5 text-sm text-gray-600 dark:text-dark-300">
              <li v-if="docUrl">
                <a :href="docUrl" target="_blank" rel="noopener noreferrer" class="transition-colors hover:text-cyan-600 dark:hover:text-cyan-400">
                  {{ t('home.docs') }} (API Docs)
                </a>
              </li>
              <li>
                <a :href="githubUrl" target="_blank" rel="noopener noreferrer" class="transition-colors hover:text-cyan-600 dark:hover:text-cyan-400">
                  GitHub Repository
                </a>
              </li>
              <li>
                <router-link :to="isAuthenticated ? dashboardPath : '/login'" class="transition-colors hover:text-cyan-600 dark:hover:text-cyan-400">
                  Hướng Dẫn Cursor &amp; Claude Code
                </router-link>
              </li>
              <li>
                <span class="text-gray-400">Python &amp; REST SDKs</span>
              </li>
            </ul>
          </div>

          <!-- Col 4: Legal & Security -->
          <div>
            <h4 class="mb-4 font-mono text-xs font-semibold tracking-wider uppercase text-gray-500 dark:text-gray-400">
              Bảo Mật &amp; Pháp Lý
            </h4>
            <ul class="space-y-2.5 text-sm text-gray-600 dark:text-dark-300">
              <li>
                <span class="text-gray-500 dark:text-gray-400">Zero-Log Architecture</span>
              </li>
              <li>
                <span class="text-gray-500 dark:text-gray-400">Secret Redaction Policy</span>
              </li>
              <li>
                <span class="text-gray-500 dark:text-gray-400">Điều Khoản Dịch Vụ</span>
              </li>
              <li>
                <span class="text-gray-500 dark:text-gray-400">SLA Cam Kết 99.99%</span>
              </li>
            </ul>
          </div>
        </div>

        <!-- Sub-footer (Bottom Bar) -->
        <div class="mt-12 flex flex-col items-center justify-between gap-4 border-t border-gray-200/80 pt-8 sm:flex-row dark:border-white/5">
          <p class="font-mono text-xs text-gray-500 dark:text-dark-400">
            &copy; {{ currentYear }} {{ siteName }}. {{ t('home.footer.allRightsReserved') }}
          </p>

          <div class="flex items-center gap-3">
            <LocaleSwitcher />
            <button
              id="footer-theme-toggle-btn"
              @click="toggleTheme"
              class="rounded-xl p-2 text-gray-500 transition-colors hover:bg-gray-200/60 hover:text-gray-700 dark:text-dark-400 dark:hover:bg-white/10 dark:hover:text-white"
              :title="isDark ? t('home.switchToLight') : t('home.switchToDark')"
            >
              <Icon v-if="isDark" name="sun" size="sm" />
              <Icon v-else name="moon" size="sm" />
            </button>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useI18n } from 'vue-i18n'
import { useAuthStore, useAppStore } from '@/stores'
import LocaleSwitcher from '@/components/common/LocaleSwitcher.vue'
import Icon from '@/components/icons/Icon.vue'
import AiGatewayGlobe3D from '@/components/home/AiGatewayGlobe3D.vue'
import ModelLogoCloud from '@/components/home/ModelLogoCloud.vue'
import BentoFeatures from '@/components/home/BentoFeatures.vue'
import ModelMatrix from '@/components/home/ModelMatrix.vue'
import QuickOnboarding from '@/components/home/QuickOnboarding.vue'
import { sanitizeUrl } from '@/utils/url'
import { FeatureFlags, isFeatureFlagEnabled } from '@/utils/featureFlags'

const { t } = useI18n()

const authStore = useAuthStore()
const appStore = useAppStore()

// Site settings - directly from appStore (already initialized from injected config)
const siteName = computed(() => appStore.cachedPublicSettings?.site_name || appStore.siteName || 'OmniAPI')
const siteLogo = computed(() => sanitizeUrl(appStore.cachedPublicSettings?.site_logo || appStore.siteLogo || '', { allowRelative: true, allowDataUrl: true }))
const siteSubtitle = computed(() => appStore.cachedPublicSettings?.site_subtitle || 'AI API Gateway Platform')
const docUrl = computed(() => sanitizeUrl(appStore.cachedPublicSettings?.doc_url || appStore.docUrl || ''))
const apiBaseUrl = computed(() => appStore.cachedPublicSettings?.api_base_url || appStore.apiBaseUrl || '')
const homeContent = computed(() => appStore.cachedPublicSettings?.home_content || '')
const hasHomeContent = computed(() => homeContent.value.trim().length > 0)
const compactHomeEnabled = computed(() => appStore.cachedPublicSettings?.compact_home_enabled === true)
const modelPlazaEnabled = computed(() => isFeatureFlagEnabled(FeatureFlags.modelPlaza))

// Check if homeContent is a URL (for iframe display)
const isHomeContentUrl = computed(() => {
  const content = homeContent.value.trim()
  return content.startsWith('http://') || content.startsWith('https://')
})

// Theme
const isDark = ref(document.documentElement.classList.contains('dark'))

// GitHub URL
const githubUrl = 'https://github.com/Wei-Shaw/sub2api'

// Auth state
const isAuthenticated = computed(() => authStore.isAuthenticated)
const modelPlazaRequiresAuth = computed(
  () => appStore.cachedPublicSettings?.model_plaza_require_auth === true,
)
const showModelPlazaEntry = computed(
  () => modelPlazaEnabled.value && (isAuthenticated.value || !modelPlazaRequiresAuth.value),
)
const isAdmin = computed(() => authStore.isAdmin)
const dashboardPath = computed(() => isAdmin.value ? '/admin/dashboard' : '/dashboard')
const userInitial = computed(() => {
  const user = authStore.user
  if (!user || !user.email) return ''
  return user.email.charAt(0).toUpperCase()
})

// Current year for footer
const currentYear = computed(() => new Date().getFullYear())

// Toggle theme
function toggleTheme() {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
}

// Initialize theme
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

onMounted(() => {
  initTheme()

  // Check auth state
  authStore.checkAuth()

  // Ensure public settings are loaded (will use cache if already loaded from injected config)
  if (!appStore.publicSettingsLoaded) {
    appStore.fetchPublicSettings()
  }
})
</script>

