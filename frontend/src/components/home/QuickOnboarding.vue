<script setup lang="ts">
import { ref, reactive, computed } from 'vue'
import { useI18n } from 'vue-i18n'
import Icon from '@/components/icons/Icon.vue'

const { t } = useI18n()

const props = withDefaults(
  defineProps<{
    apiBaseUrl?: string
    isAuthenticated?: boolean
    dashboardPath?: string
  }>(),
  {
    apiBaseUrl: '',
    isAuthenticated: false,
    dashboardPath: '/dashboard'
  }
)

const effectiveBaseUrl = computed(() => {
  if (props.apiBaseUrl && props.apiBaseUrl.trim()) {
    return props.apiBaseUrl.replace(/\/+$/, '')
  }
  if (typeof window !== 'undefined') {
    return window.location.origin
  }
  return 'http://localhost:3000'
})

// 3D Tilt for 3 cards
interface CardTransform {
  transform: string
  mouseX: number
  mouseY: number
  isHovered: boolean
}

const cardTransforms = reactive<Record<number, CardTransform>>({
  1: { transform: '', mouseX: 0, mouseY: 0, isHovered: false },
  2: { transform: '', mouseX: 0, mouseY: 0, isHovered: false },
  3: { transform: '', mouseX: 0, mouseY: 0, isHovered: false }
})

function handleMouseMove(e: MouseEvent, cardId: number) {
  const card = e.currentTarget as HTMLElement
  if (!card) return

  const rect = card.getBoundingClientRect()
  const x = e.clientX - rect.left
  const y = e.clientY - rect.top
  const centerX = rect.width / 2
  const centerY = rect.height / 2

  const rotateX = -((y - centerY) / centerY) * 6
  const rotateY = ((x - centerX) / centerX) * 6

  cardTransforms[cardId] = {
    transform: `perspective(1000px) rotateX(${rotateX.toFixed(2)}deg) rotateY(${rotateY.toFixed(2)}deg) scale3d(1.01, 1.01, 1.01)`,
    mouseX: x,
    mouseY: y,
    isHovered: true
  }
}

function handleMouseLeave(cardId: number) {
  cardTransforms[cardId] = {
    transform: 'perspective(1000px) rotateX(0deg) rotateY(0deg) scale3d(1, 1, 1)',
    mouseX: 0,
    mouseY: 0,
    isHovered: false
  }
}

// ==========================================
// CARD 1: Key Generator Simulator
// ==========================================
const mockKeySuffix = ref('9x4a')
const isGeneratingKey = ref(false)
const copiedKey = ref(false)

function generateNewKey() {
  isGeneratingKey.value = true
  const randomChars = Math.random().toString(36).substring(2, 6)
  setTimeout(() => {
    mockKeySuffix.value = randomChars
    isGeneratingKey.value = false
  }, 400)
}

async function copyMockKey() {
  try {
    await navigator.clipboard.writeText(`sk-sub2api-live${mockKeySuffix.value}7f02`)
    copiedKey.value = true
    setTimeout(() => {
      copiedKey.value = false
    }, 2000)
  } catch (err) {
    console.error('Copy key error', err)
  }
}

// ==========================================
// CARD 2: Multi-IDE Config Switcher & Ping
// ==========================================
type IdeTab = 'cursor' | 'claude' | 'python'
const activeIdeTab = ref<IdeTab>('cursor')
const copiedConfig = ref(false)
const isPinging = ref(false)
const pingResult = ref<{ latency: number; ok: boolean } | null>(null)

const currentSnippet = computed(() => {
  const url = `${effectiveBaseUrl.value}/v1`
  if (activeIdeTab.value === 'cursor') {
    return `// .cursorrules hoặc Cursor Settings\nBase URL: "${url}"\nAPI Key:  "sk-sub2api-..."`
  }
  if (activeIdeTab.value === 'claude') {
    return `# Chạy lệnh thiết lập trong Terminal\nexport ANTHROPIC_BASE_URL="${url}"\nclaude --model claude-3-7-sonnet`
  }
  return `# Python OpenAI SDK\nfrom openai import OpenAI\nclient = OpenAI(base_url="${url}", api_key="sk-...")`
})

async function copyConfigSnippet() {
  try {
    await navigator.clipboard.writeText(currentSnippet.value)
    copiedConfig.value = true
    setTimeout(() => {
      copiedConfig.value = false
    }, 2000)
  } catch (err) {
    console.error('Copy snippet error', err)
  }
}

function testPing() {
  isPinging.value = true
  pingResult.value = null
  setTimeout(() => {
    isPinging.value = false
    pingResult.value = {
      latency: Math.floor(Math.random() * 12) + 14,
      ok: true
    }
  }, 600)
}

// ==========================================
// CARD 3: Live Output & Waveform Simulation
// ==========================================
const isStreamingSim = ref(false)
const streamedChars = ref(100)

function triggerStreamSim() {
  if (isStreamingSim.value) return
  isStreamingSim.value = true
  streamedChars.value = 10
  const interval = setInterval(() => {
    streamedChars.value += 15
    if (streamedChars.value >= 100) {
      clearInterval(interval)
      isStreamingSim.value = false
    }
  }, 100)
}
</script>

<template>
  <section class="relative py-8">
    <!-- Header Section -->
    <div class="mb-14 text-center">
      <div class="mb-3.5 inline-flex items-center gap-2 rounded-full border border-emerald-500/30 bg-emerald-500/10 px-4 py-1.5 text-xs font-semibold text-emerald-500 backdrop-blur-md dark:text-emerald-400">
        <span class="relative flex h-2 w-2">
          <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-emerald-400 opacity-75"></span>
          <span class="relative inline-flex h-2 w-2 rounded-full bg-emerald-500"></span>
        </span>
        <span>Bắt Đầu Nhanh Trong 60 Giây &bull; Quick Onboarding</span>
      </div>
      <h2 class="text-3xl font-extrabold tracking-tight text-gray-900 dark:text-white sm:text-4xl md:text-5xl">
        Chỉ 3 Bước Đơn Giản Để Sử Dụng Toàn Bộ Siêu AI
      </h2>
      <p class="mx-auto mt-4 max-w-2xl text-base text-gray-600 dark:text-dark-300">
        Không cần cấu hình phức tạp. Tích hợp trực tiếp vào mọi IDE và công cụ lập trình yêu thích của bạn với độ ổn định 99.99%.
      </p>
    </div>

    <!-- Interactive Connecting Circuit Bar (Desktop Only) -->
    <div class="relative mx-auto mb-6 hidden max-w-5xl px-16 lg:block">
      <div class="relative flex items-center justify-between">
        <!-- Connecting Line Background -->
        <div class="absolute left-10 right-10 top-1/2 h-[2px] -translate-y-1/2 bg-gradient-to-r from-cyan-500/20 via-violet-500/20 to-emerald-500/20"></div>

        <!-- Moving Animated Laser Pulse Photon -->
        <div class="absolute left-10 right-10 top-1/2 h-[2px] -translate-y-1/2 overflow-hidden">
          <div class="h-full w-32 animate-[shimmer_3s_infinite] bg-gradient-to-r from-transparent via-cyan-400 to-transparent"></div>
        </div>

        <!-- Step 1 Node Indicator -->
        <div class="relative z-10 flex items-center gap-2 rounded-full border border-cyan-500/30 bg-white/90 px-3 py-1 font-mono text-xs font-bold text-cyan-600 shadow-lg shadow-cyan-500/15 backdrop-blur-md dark:bg-dark-900/90 dark:text-cyan-400">
          <span class="h-2 w-2 rounded-full bg-cyan-400 animate-pulse"></span>
          <span>Bước 01 &bull; Cấp Khóa</span>
        </div>

        <!-- Step 2 Node Indicator -->
        <div class="relative z-10 flex items-center gap-2 rounded-full border border-violet-500/30 bg-white/90 px-3 py-1 font-mono text-xs font-bold text-violet-600 shadow-lg shadow-violet-500/15 backdrop-blur-md dark:bg-dark-900/90 dark:text-violet-400">
          <span class="h-2 w-2 rounded-full bg-violet-400 animate-pulse"></span>
          <span>Bước 02 &bull; Cấu Hình IDE</span>
        </div>

        <!-- Step 3 Node Indicator -->
        <div class="relative z-10 flex items-center gap-2 rounded-full border border-emerald-500/30 bg-white/90 px-3 py-1 font-mono text-xs font-bold text-emerald-600 shadow-lg shadow-emerald-500/15 backdrop-blur-md dark:bg-dark-900/90 dark:text-emerald-400">
          <span class="h-2 w-2 rounded-full bg-emerald-400 animate-pulse"></span>
          <span>Bước 03 &bull; Tăng Tốc</span>
        </div>
      </div>
    </div>

    <!-- 3 Animated Interactive Cards Grid -->
    <div class="grid grid-cols-1 gap-6 lg:grid-cols-3">
      <!-- ============================================================== -->
      <!-- CARD 1: ĐĂNG KÝ & SINH API KEY                                 -->
      <!-- ============================================================== -->
      <div
        class="group relative flex flex-col justify-between overflow-hidden rounded-3xl border border-gray-200/80 bg-white/80 p-6 shadow-xl backdrop-blur-xl transition-all duration-300 dark:border-white/10 dark:bg-dark-900/80 sm:p-8"
        :style="{
          transform: cardTransforms[1].transform,
          transition: cardTransforms[1].isHovered ? 'transform 0.1s ease-out' : 'transform 0.4s ease-out'
        }"
        @mousemove="handleMouseMove($event, 1)"
        @mouseleave="handleMouseLeave(1)"
      >
        <!-- Radial Spotlight Glow -->
        <div
          v-if="cardTransforms[1].isHovered"
          class="pointer-events-none absolute -inset-px rounded-3xl opacity-60 transition-opacity duration-300"
          :style="{
            background: `radial-gradient(350px circle at ${cardTransforms[1].mouseX}px ${cardTransforms[1].mouseY}px, rgba(6, 182, 212, 0.18), transparent 70%)`
          }"
        ></div>

        <!-- Ambient Color Orb -->
        <div class="pointer-events-none absolute -right-16 -top-16 h-48 w-48 rounded-full bg-cyan-500/15 blur-3xl dark:bg-cyan-500/20"></div>

        <div>
          <!-- Top Badge & Step Number -->
          <div class="mb-5 flex items-center justify-between">
            <div class="relative flex h-14 w-14 items-center justify-center rounded-2xl bg-gradient-to-br from-cyan-400 to-blue-600 font-mono text-2xl font-black text-white shadow-lg shadow-cyan-500/30 transition-transform duration-300 group-hover:scale-105">
              <span>01</span>
              <div class="absolute -inset-1 rounded-2xl bg-cyan-400/20 blur-sm -z-10 group-hover:bg-cyan-400/40"></div>
            </div>

            <span class="inline-flex items-center gap-1.5 rounded-full border border-cyan-500/30 bg-cyan-500/10 px-3 py-1 font-mono text-[11px] font-semibold text-cyan-600 dark:text-cyan-400">
              <span class="h-1.5 w-1.5 rounded-full bg-cyan-400 animate-ping"></span>
              <span>10 Giây Khởi Tạo</span>
            </span>
          </div>

          <!-- Title & Description -->
          <h3 class="mb-2 text-xl font-bold text-gray-900 transition-colors group-hover:text-cyan-600 dark:text-white dark:group-hover:text-cyan-400">
            Đăng Ký &amp; Sinh API Key
          </h3>
          <p class="text-sm leading-relaxed text-gray-600 dark:text-dark-300">
            Khởi tạo tài khoản tức thì, cấp phát khóa bảo mật không giới hạn và nhận ngay hạn ngạch dùng thử cho mọi siêu mô hình AI.
          </p>

          <!-- Interactive Simulated Key Card -->
          <div class="mt-6 overflow-hidden rounded-2xl border border-gray-200/90 bg-gray-50/90 p-4 font-mono shadow-inner transition-all dark:border-white/10 dark:bg-dark-950/70">
            <div class="mb-2.5 flex items-center justify-between text-[11px] text-gray-500 dark:text-gray-400">
              <span class="flex items-center gap-1.5 font-semibold text-gray-700 dark:text-gray-300">
                <span class="h-2 w-2 rounded-full bg-emerald-400 animate-pulse"></span>
                <span>Sub2API Security Key</span>
              </span>
              <button
                @click="generateNewKey"
                class="flex items-center gap-1 rounded-md px-1.5 py-0.5 text-cyan-600 transition-colors hover:bg-cyan-500/10 dark:text-cyan-400"
                :title="'Tạo lại khóa mô phỏng'"
              >
                <svg class="h-3 w-3" :class="{ 'animate-spin': isGeneratingKey }" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
                </svg>
                <span>Sinh lại</span>
              </button>
            </div>

            <!-- Key Display Box with Scramble Flash -->
            <div class="flex items-center justify-between rounded-xl border border-gray-200 bg-white px-3 py-2 text-xs transition-all dark:border-white/10 dark:bg-dark-900">
              <span class="font-mono text-cyan-600 transition-all dark:text-cyan-300" :class="{ 'opacity-50 blur-xs': isGeneratingKey }">
                sk-sub2api-••••••{{ mockKeySuffix }}
              </span>
              <button
                @click="copyMockKey"
                class="rounded-lg bg-cyan-500/10 px-2 py-1 text-[11px] font-semibold text-cyan-600 transition-colors hover:bg-cyan-500/20 active:scale-95 dark:text-cyan-300"
              >
                {{ copiedKey ? '✓ Đã chép' : 'Copy' }}
              </button>
            </div>
          </div>
        </div>

        <!-- Card Footer Status -->
        <div class="mt-6 flex items-center justify-between border-t border-gray-100 pt-4 font-mono text-xs text-gray-500 dark:border-white/5 dark:text-dark-400">
          <span class="flex items-center gap-1.5 text-emerald-600 dark:text-emerald-400 font-semibold">
            <span>✔</span> Cấp phát tức thì
          </span>
          <span>0 thẻ tín dụng</span>
        </div>
      </div>

      <!-- ============================================================== -->
      <!-- CARD 2: CẤU HÌNH BASE URL & IDE                                -->
      <!-- ============================================================== -->
      <div
        class="group relative flex flex-col justify-between overflow-hidden rounded-3xl border border-gray-200/80 bg-white/80 p-6 shadow-xl backdrop-blur-xl transition-all duration-300 dark:border-white/10 dark:bg-dark-900/80 sm:p-8"
        :style="{
          transform: cardTransforms[2].transform,
          transition: cardTransforms[2].isHovered ? 'transform 0.1s ease-out' : 'transform 0.4s ease-out'
        }"
        @mousemove="handleMouseMove($event, 2)"
        @mouseleave="handleMouseLeave(2)"
      >
        <!-- Radial Spotlight Glow -->
        <div
          v-if="cardTransforms[2].isHovered"
          class="pointer-events-none absolute -inset-px rounded-3xl opacity-60 transition-opacity duration-300"
          :style="{
            background: `radial-gradient(350px circle at ${cardTransforms[2].mouseX}px ${cardTransforms[2].mouseY}px, rgba(139, 92, 246, 0.18), transparent 70%)`
          }"
        ></div>

        <!-- Ambient Color Orb -->
        <div class="pointer-events-none absolute -right-16 -top-16 h-48 w-48 rounded-full bg-violet-500/15 blur-3xl dark:bg-violet-500/20"></div>

        <div>
          <!-- Top Badge & Step Number -->
          <div class="mb-5 flex items-center justify-between">
            <div class="relative flex h-14 w-14 items-center justify-center rounded-2xl bg-gradient-to-br from-violet-500 to-purple-600 font-mono text-2xl font-black text-white shadow-lg shadow-violet-500/30 transition-transform duration-300 group-hover:scale-105">
              <span>02</span>
              <div class="absolute -inset-1 rounded-2xl bg-violet-400/20 blur-sm -z-10 group-hover:bg-violet-400/40"></div>
            </div>

            <span class="inline-flex items-center gap-1.5 rounded-full border border-violet-500/30 bg-violet-500/10 px-3 py-1 font-mono text-[11px] font-semibold text-violet-600 dark:text-violet-400">
              <span class="h-1.5 w-1.5 rounded-full bg-violet-400 animate-ping"></span>
              <span>1 Bước Trỏ URL</span>
            </span>
          </div>

          <!-- Title & Description -->
          <h3 class="mb-2 text-xl font-bold text-gray-900 transition-colors group-hover:text-violet-600 dark:text-white dark:group-hover:text-violet-400">
            Cấu Hình Base URL
          </h3>
          <p class="text-sm leading-relaxed text-gray-600 dark:text-dark-300">
            Trỏ biến môi trường hoặc endpoint trong Cursor, Claude Code, Cline hoặc Python về địa chỉ Sub2API Gateway.
          </p>

          <!-- Interactive Multi-IDE Tab Switcher -->
          <div class="mt-6 overflow-hidden rounded-2xl border border-gray-200/90 bg-gray-50/90 font-mono shadow-inner dark:border-white/10 dark:bg-dark-950/70">
            <!-- IDE Tabs -->
            <div class="flex border-b border-gray-200/80 bg-gray-100/70 p-1 text-[11px] dark:border-white/10 dark:bg-dark-900/60">
              <button
                @click="activeIdeTab = 'cursor'"
                class="flex-1 rounded-lg py-1.5 text-center font-medium transition-all"
                :class="activeIdeTab === 'cursor' ? 'bg-white text-violet-600 shadow-sm dark:bg-dark-800 dark:text-violet-300' : 'text-gray-500 hover:text-gray-800 dark:text-gray-400 dark:hover:text-white'"
              >
                Cursor / Cline
              </button>
              <button
                @click="activeIdeTab = 'claude'"
                class="flex-1 rounded-lg py-1.5 text-center font-medium transition-all"
                :class="activeIdeTab === 'claude' ? 'bg-white text-violet-600 shadow-sm dark:bg-dark-800 dark:text-violet-300' : 'text-gray-500 hover:text-gray-800 dark:text-gray-400 dark:hover:text-white'"
              >
                Claude CLI
              </button>
              <button
                @click="activeIdeTab = 'python'"
                class="flex-1 rounded-lg py-1.5 text-center font-medium transition-all"
                :class="activeIdeTab === 'python' ? 'bg-white text-violet-600 shadow-sm dark:bg-dark-800 dark:text-violet-300' : 'text-gray-500 hover:text-gray-800 dark:text-gray-400 dark:hover:text-white'"
              >
                Python SDK
              </button>
            </div>

            <!-- Snippet Body with Copy -->
            <div class="relative p-3.5">
              <pre class="overflow-x-auto text-[11px] leading-relaxed text-gray-700 dark:text-dark-200">{{ currentSnippet }}</pre>

              <div class="mt-3 flex items-center justify-between border-t border-gray-200/60 pt-2.5 dark:border-white/5">
                <button
                  @click="testPing"
                  class="flex items-center gap-1.5 text-[11px] font-semibold text-violet-600 transition-colors hover:text-violet-700 dark:text-violet-400 dark:hover:text-violet-300"
                >
                  <span class="h-2 w-2 rounded-full" :class="isPinging ? 'bg-amber-400 animate-ping' : pingResult ? 'bg-emerald-400' : 'bg-gray-400'"></span>
                  <span>{{ isPinging ? 'Đang test...' : pingResult ? `${pingResult.latency}ms • 200 OK` : 'Ping Endpoint' }}</span>
                </button>

                <button
                  @click="copyConfigSnippet"
                  class="rounded-lg bg-violet-500/10 px-2.5 py-1 text-[11px] font-semibold text-violet-600 transition-colors hover:bg-violet-500/20 active:scale-95 dark:text-violet-300"
                >
                  {{ copiedConfig ? '✓ Đã chép' : 'Sao chép cấu hình' }}
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Card Footer -->
        <div class="mt-6 flex items-center justify-between border-t border-gray-100 pt-4 font-mono text-xs text-gray-500 dark:border-white/5 dark:text-dark-400">
          <span class="text-violet-600 dark:text-violet-400 font-semibold truncate max-w-[200px]">
            {{ effectiveBaseUrl }}/v1
          </span>
          <span class="text-emerald-500 font-semibold">Tương thích 100%</span>
        </div>
      </div>

      <!-- ============================================================== -->
      <!-- CARD 3: LẬP TRÌNH KHÔNG GIỚI HẠN                               -->
      <!-- ============================================================== -->
      <div
        class="group relative flex flex-col justify-between overflow-hidden rounded-3xl border border-gray-200/80 bg-white/80 p-6 shadow-xl backdrop-blur-xl transition-all duration-300 dark:border-white/10 dark:bg-dark-900/80 sm:p-8"
        :style="{
          transform: cardTransforms[3].transform,
          transition: cardTransforms[3].isHovered ? 'transform 0.1s ease-out' : 'transform 0.4s ease-out'
        }"
        @mousemove="handleMouseMove($event, 3)"
        @mouseleave="handleMouseLeave(3)"
      >
        <!-- Radial Spotlight Glow -->
        <div
          v-if="cardTransforms[3].isHovered"
          class="pointer-events-none absolute -inset-px rounded-3xl opacity-60 transition-opacity duration-300"
          :style="{
            background: `radial-gradient(350px circle at ${cardTransforms[3].mouseX}px ${cardTransforms[3].mouseY}px, rgba(16, 185, 129, 0.18), transparent 70%)`
          }"
        ></div>

        <!-- Ambient Color Orb -->
        <div class="pointer-events-none absolute -right-16 -top-16 h-48 w-48 rounded-full bg-emerald-500/15 blur-3xl dark:bg-emerald-500/20"></div>

        <div>
          <!-- Top Badge & Step Number -->
          <div class="mb-5 flex items-center justify-between">
            <div class="relative flex h-14 w-14 items-center justify-center rounded-2xl bg-gradient-to-br from-emerald-500 to-teal-600 font-mono text-2xl font-black text-white shadow-lg shadow-emerald-500/30 transition-transform duration-300 group-hover:scale-105">
              <span>03</span>
              <div class="absolute -inset-1 rounded-2xl bg-emerald-400/20 blur-sm -z-10 group-hover:bg-emerald-400/40"></div>
            </div>

            <span class="inline-flex items-center gap-1.5 rounded-full border border-emerald-500/30 bg-emerald-500/10 px-3 py-1 font-mono text-[11px] font-semibold text-emerald-600 dark:text-emerald-400">
              <span class="h-1.5 w-1.5 rounded-full bg-emerald-400 animate-ping"></span>
              <span>Không Rớt Kết Nối</span>
            </span>
          </div>

          <!-- Title & Description -->
          <h3 class="mb-2 text-xl font-bold text-gray-900 transition-colors group-hover:text-emerald-600 dark:text-white dark:group-hover:text-emerald-400">
            Lập Trình Không Giới Hạn
          </h3>
          <p class="text-sm leading-relaxed text-gray-600 dark:text-dark-300">
            Tận hưởng sức mạnh của Claude 3.7 Thinking, o3-mini, Gemini 2.5 Pro mà không lo cạn hạn ngạch hay đứt đoạn phiên code.
          </p>

          <!-- Interactive Live Stream Simulation -->
          <div class="mt-6 overflow-hidden rounded-2xl border border-gray-200/90 bg-gray-50/90 p-4 font-mono shadow-inner dark:border-white/10 dark:bg-dark-950/70">
            <div class="mb-2 flex items-center justify-between text-[11px] text-gray-500 dark:text-gray-400">
              <span class="font-semibold text-gray-700 dark:text-gray-300">Live Gateway Output</span>
              <button
                @click="triggerStreamSim"
                class="rounded bg-emerald-500/10 px-2 py-0.5 text-emerald-600 transition-colors hover:bg-emerald-500/20 dark:text-emerald-400"
              >
                {{ isStreamingSim ? 'Đang stream...' : 'Thử stream' }}
              </button>
            </div>

            <!-- Mini Live Console -->
            <div class="space-y-1.5 rounded-xl border border-gray-200 bg-white p-3 text-[11px] dark:border-white/10 dark:bg-dark-900">
              <div class="flex items-center justify-between text-gray-600 dark:text-gray-400">
                <span>Prompt Cache:</span>
                <span class="font-bold text-cyan-600 dark:text-cyan-400">94.2% Hit (-80% fee)</span>
              </div>
              <div class="flex items-center justify-between text-gray-600 dark:text-gray-400">
                <span>Failover Auto:</span>
                <span class="font-bold text-emerald-600 dark:text-emerald-400">&lt; 20ms (Zero Drop)</span>
              </div>
              <div class="flex items-center justify-between text-gray-600 dark:text-gray-400">
                <span>Context State:</span>
                <span class="font-bold text-violet-600 dark:text-violet-400">1M Tokens Preserved</span>
              </div>

              <!-- Animated Equalizer Bar -->
              <div class="mt-3 flex items-center justify-center gap-1 pt-2">
                <span class="h-3 w-1 rounded-full bg-emerald-400 animate-[pulse_1s_infinite]"></span>
                <span class="h-5 w-1 rounded-full bg-cyan-400 animate-[pulse_1.2s_infinite]"></span>
                <span class="h-7 w-1 rounded-full bg-violet-400 animate-[pulse_0.8s_infinite]"></span>
                <span class="h-4 w-1 rounded-full bg-emerald-400 animate-[pulse_1.1s_infinite]"></span>
                <span class="h-6 w-1 rounded-full bg-cyan-400 animate-[pulse_0.9s_infinite]"></span>
                <span class="h-3 w-1 rounded-full bg-violet-400 animate-[pulse_1.3s_infinite]"></span>
              </div>
            </div>
          </div>
        </div>

        <!-- Card Footer -->
        <div class="mt-6 flex items-center justify-between border-t border-gray-100 pt-4 font-mono text-xs text-gray-500 dark:border-white/5 dark:text-dark-400">
          <span class="text-emerald-600 dark:text-emerald-400 font-semibold">⚡ SLA 99.99% Uptime</span>
          <span>100K+ RPS</span>
        </div>
      </div>
    </div>

    <!-- ================================================================ -->
    <!-- JAW-DROPPING CYBER CTA BANNER WITH ROTATING BORDER BEAM          -->
    <!-- ================================================================ -->
    <div class="relative mt-14 overflow-hidden rounded-3xl p-[2px]">
      <!-- Animated Rotating Glowing Border Beam -->
      <div class="absolute -inset-[100%] animate-border-beam rounded-3xl bg-[conic-gradient(from_0deg,transparent_0_300deg,#06b6d4_340deg,#10b981_360deg)] opacity-50 dark:opacity-75"></div>

      <!-- Main Banner Body Container -->
      <div class="relative overflow-hidden rounded-[22px] border border-gray-200/90 bg-gradient-to-br from-white via-cyan-50/70 to-emerald-50/60 px-6 py-12 text-center text-gray-900 shadow-xl shadow-cyan-500/10 backdrop-blur-2xl sm:px-12 sm:py-16 dark:border-transparent dark:from-gray-900 dark:via-dark-950 dark:to-gray-950 dark:text-white dark:shadow-2xl">
        <!-- Floating Aura Glow in Background -->
        <div class="pointer-events-none absolute -left-32 -top-32 h-80 w-80 rounded-full bg-cyan-500/10 blur-[100px] dark:bg-cyan-500/20"></div>
        <div class="pointer-events-none absolute -right-32 -bottom-32 h-80 w-80 rounded-full bg-emerald-500/10 blur-[100px] dark:bg-emerald-500/20"></div>
        <div class="pointer-events-none absolute left-1/2 top-1/2 h-64 w-64 -translate-x-1/2 -translate-y-1/2 rounded-full bg-violet-600/10 blur-[120px] dark:bg-violet-600/15"></div>

        <!-- Floating Feature Pills -->
        <div class="mb-6 flex flex-wrap items-center justify-center gap-2.5">
          <span class="rounded-full border border-cyan-500/30 bg-white/90 px-3.5 py-1 font-mono text-xs font-semibold text-cyan-700 shadow-sm backdrop-blur-md dark:border-cyan-500/30 dark:bg-cyan-500/10 dark:text-cyan-300">
            ⚡ &lt;30ms Failover
          </span>
          <span class="rounded-full border border-violet-500/30 bg-white/90 px-3.5 py-1 font-mono text-xs font-semibold text-violet-700 shadow-sm backdrop-blur-md dark:border-violet-500/30 dark:bg-violet-500/10 dark:text-violet-300">
            💎 Tiết Kiệm 80% Prompt Cache
          </span>
          <span class="rounded-full border border-emerald-500/30 bg-white/90 px-3.5 py-1 font-mono text-xs font-semibold text-emerald-700 shadow-sm backdrop-blur-md dark:border-emerald-500/30 dark:bg-emerald-500/10 dark:text-emerald-300">
            🛡️ Zero-Log Security
          </span>
          <span class="rounded-full border border-orange-500/30 bg-white/90 px-3.5 py-1 font-mono text-xs font-semibold text-orange-700 shadow-sm backdrop-blur-md dark:border-orange-500/30 dark:bg-orange-500/10 dark:text-orange-300">
            🚀 50+ Models Unified
          </span>
        </div>

        <h3 class="mx-auto max-w-3xl text-3xl font-extrabold tracking-tight text-gray-900 sm:text-4xl md:text-5xl dark:text-white">
          Sẵn Sàng Nâng Tầm Năng Suất Lập Trình?
        </h3>
        <p class="mx-auto mt-4 max-w-2xl text-base text-gray-600 sm:text-lg dark:text-gray-300">
          Bắt đầu với tài khoản dùng thử và trải nghiệm tốc độ phản hồi vượt bậc của hệ sinh thái Sub2API ngay hôm nay.
        </p>

        <!-- CTA Magnetic Glowing Button -->
        <div class="mt-8 flex flex-col items-center justify-center gap-4 sm:flex-row">
          <router-link
            :to="isAuthenticated ? dashboardPath : '/login'"
            class="group relative inline-flex items-center gap-2.5 overflow-hidden rounded-2xl bg-gradient-to-r from-cyan-500 via-primary-500 to-emerald-500 px-9 py-4 font-mono text-base font-bold text-white shadow-xl shadow-cyan-500/30 transition-all duration-300 hover:scale-105 hover:shadow-cyan-500/50 active:scale-95"
          >
            <!-- Shimmer Light Sweep on Button -->
            <span class="absolute inset-0 -translate-x-full bg-gradient-to-r from-transparent via-white/25 to-transparent transition-transform duration-700 group-hover:translate-x-full"></span>
            
            <span>{{ isAuthenticated ? t('home.goToDashboard') : 'Khởi Tạo Khóa Miễn Phí Ngay' }}</span>
            <Icon name="arrowRight" size="md" :stroke-width="2.5" class="transition-transform duration-300 group-hover:translate-x-1" />
          </router-link>
        </div>

        <p class="mt-4 font-mono text-xs text-gray-500 dark:text-gray-400">
          Không yêu cầu thẻ tín dụng &bull; Tích hợp trong 60 giây &bull; SLA 99.99%
        </p>
      </div>
    </div>
  </section>
</template>
