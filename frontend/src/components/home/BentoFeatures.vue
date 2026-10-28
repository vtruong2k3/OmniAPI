<script setup lang="ts">
import { ref, reactive } from 'vue'

interface CardTransform {
  transform: string
  mouseX: number
  mouseY: number
  isHovered: boolean
}

// Track 3D tilt and mouse spotlight for 4 cards
const cardTransforms = reactive<Record<number, CardTransform>>({
  1: { transform: '', mouseX: 0, mouseY: 0, isHovered: false },
  2: { transform: '', mouseX: 0, mouseY: 0, isHovered: false },
  3: { transform: '', mouseX: 0, mouseY: 0, isHovered: false },
  4: { transform: '', mouseX: 0, mouseY: 0, isHovered: false }
})

function handleMouseMove(e: MouseEvent, cardId: number) {
  const card = e.currentTarget as HTMLElement
  if (!card) return

  const rect = card.getBoundingClientRect()
  const x = e.clientX - rect.left
  const y = e.clientY - rect.top

  const centerX = rect.width / 2
  const centerY = rect.height / 2

  // Subtle 3D tilt angle (max 5 deg)
  const rotateX = -((y - centerY) / centerY) * 5
  const rotateY = ((x - centerX) / centerX) * 5

  cardTransforms[cardId] = {
    transform: `perspective(1000px) rotateX(${rotateX.toFixed(2)}deg) rotateY(${rotateY.toFixed(2)}deg) scale3d(1.008, 1.008, 1.008)`,
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

// Active tab for failover demonstration
const failoverSimActive = ref(true)
function triggerFailoverToggle() {
  failoverSimActive.value = !failoverSimActive.value
}
</script>

<template>
  <section class="relative py-8">
    <!-- Section Header -->
    <div class="mb-12 text-center">
      <div class="mb-3 inline-flex items-center gap-2 rounded-full border border-violet-500/30 bg-violet-500/10 px-3.5 py-1 text-xs font-semibold text-violet-400 backdrop-blur-md">
        <span class="h-1.5 w-1.5 rounded-full bg-violet-400 animate-ping"></span>
        <span>Kiến Trúc Đột Phá • Bento Architecture 2.0</span>
      </div>
      <h2 class="text-3xl font-extrabold tracking-tight text-gray-900 dark:text-white sm:text-4xl">
        Được Thiết Kế Cho Độ Ổn Định Tuyệt Đối
      </h2>
      <p class="mx-auto mt-3 max-w-2xl text-base text-gray-600 dark:text-dark-300">
        Giải quyết triệt để các rào cản quota, suy giảm ngữ cảnh và chi phí API đắt đỏ với công nghệ định tuyến lượng tử độc quyền.
      </p>
    </div>

    <!-- Asymmetrical 12-Column Bento Grid -->
    <div class="grid grid-cols-1 gap-6 lg:grid-cols-12">
      <!-- ======================================================== -->
      <!-- CARD 1: Sticky Session Routing (Col Span 7)              -->
      <!-- ======================================================== -->
      <div
        class="group relative flex flex-col justify-between overflow-hidden rounded-3xl border border-gray-200/80 bg-white/80 p-6 shadow-xl backdrop-blur-xl transition-all duration-300 dark:border-white/10 dark:bg-dark-900/80 lg:col-span-7 sm:p-8"
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
            background: `radial-gradient(450px circle at ${cardTransforms[1].mouseX}px ${cardTransforms[1].mouseY}px, rgba(6, 182, 212, 0.15), transparent 70%)`
          }"
        ></div>

        <!-- Ambient Background Glow -->
        <div class="pointer-events-none absolute -right-20 -top-20 h-64 w-64 rounded-full bg-cyan-500/10 blur-3xl dark:bg-cyan-500/20"></div>

        <div>
          <!-- Header Badge -->
          <div class="mb-4 flex items-center justify-between">
            <span class="inline-flex items-center gap-1.5 rounded-xl border border-cyan-500/30 bg-cyan-500/10 px-3 py-1 font-mono text-xs font-semibold text-cyan-600 dark:text-cyan-400">
              <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z" />
              </svg>
              Sticky Session 1M
            </span>
            <span class="font-mono text-xs text-gray-500 dark:text-dark-400">80% Cache Saving</span>
          </div>

          <h3 class="text-2xl font-bold tracking-tight text-gray-900 dark:text-white">
            Bảo Toàn Ngữ Cảnh 1M &amp; Tối Ưu Prompt Cache
          </h3>
          <p class="mt-2.5 text-sm leading-relaxed text-gray-600 dark:text-dark-300">
            Hệ thống định tuyến thông minh gán chặt phiên làm việc (Session Persistence) vào cùng một tài khoản upstream, giữ nguyên vẹn cache 1M token giữa các câu hỏi liên tiếp mà không bị phân tán.
          </p>
        </div>

        <!-- Visual Flow Diagram Widget -->
        <div class="relative mt-6 rounded-2xl border border-gray-200/80 bg-gray-50/90 p-5 shadow-inner dark:border-cyan-500/20 dark:bg-dark-950/80">
          <div class="flex flex-col items-center justify-between gap-4 sm:flex-row">
            <!-- Client Session Box -->
            <div class="flex flex-1 items-center gap-3 rounded-xl border border-gray-200 bg-white p-3 text-left shadow-sm dark:border-white/10 dark:bg-white/5">
              <div class="flex h-10 w-10 shrink-0 items-center justify-center rounded-lg bg-cyan-500/15 text-cyan-600 dark:bg-cyan-500/20 dark:text-cyan-400">
                <span class="font-mono text-sm font-bold">CLI</span>
              </div>
              <div class="min-w-0">
                <div class="truncate font-mono text-xs font-semibold text-gray-900 dark:text-white">Claude Code / Cursor</div>
                <div class="flex items-center gap-1 text-[11px] text-cyan-600 dark:text-cyan-400 font-medium">
                  <span class="h-1.5 w-1.5 rounded-full bg-cyan-500 dark:bg-cyan-400 animate-pulse"></span>
                  Session #88BA9B
                </div>
              </div>
            </div>

            <!-- Signal Bridge Arrow -->
            <div class="flex flex-col items-center justify-center text-cyan-600 dark:text-cyan-400">
              <span class="font-mono text-[10px] text-gray-500 dark:text-gray-400">Sticky Lock</span>
              <div class="flex items-center gap-1 font-mono text-xs">
                <span>━━</span>
                <span class="animate-pulse">▶</span>
                <span>━━</span>
              </div>
            </div>

            <!-- Upstream Account Box -->
            <div class="flex flex-1 items-center gap-3 rounded-xl border border-emerald-500/30 bg-emerald-50/70 p-3 text-left shadow-sm dark:border-emerald-500/20 dark:bg-emerald-500/5">
              <div class="flex h-10 w-10 shrink-0 items-center justify-center rounded-lg bg-emerald-500/15 text-emerald-600 dark:bg-emerald-500/20 dark:text-emerald-400">
                <span class="font-mono text-sm font-bold">PRO</span>
              </div>
              <div class="min-w-0">
                <div class="truncate font-mono text-xs font-semibold text-gray-900 dark:text-white">Anthropic Upstream #2</div>
                <div class="text-[11px] text-emerald-600 dark:text-emerald-400 font-mono font-medium">100% Cache Lock</div>
              </div>
            </div>
          </div>

          <!-- Metric Highlights Bar -->
          <div class="mt-4 flex flex-wrap items-center justify-between gap-2 border-t border-gray-200/80 pt-3 font-mono text-xs dark:border-white/5">
            <span class="text-gray-600 dark:text-gray-400">Cache Hit Rate: <strong class="font-bold text-cyan-600 dark:text-cyan-400">88% - 94%</strong></span>
            <span class="rounded bg-cyan-500/10 px-2 py-0.5 text-[11px] font-semibold text-cyan-700 dark:text-cyan-300">TTFT: &lt; 28ms</span>
            <span class="font-semibold text-emerald-600 dark:text-emerald-400">Tiết kiệm: $0.0034 / turn</span>
          </div>
        </div>
      </div>

      <!-- ======================================================== -->
      <!-- CARD 2: Zero-Downtime Automatic Failover (Col Span 5)    -->
      <!-- ======================================================== -->
      <div
        class="group relative flex flex-col justify-between overflow-hidden rounded-3xl border border-gray-200/80 bg-white/80 p-6 shadow-xl backdrop-blur-xl transition-all duration-300 dark:border-white/10 dark:bg-dark-900/80 lg:col-span-5 sm:p-8"
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
            background: `radial-gradient(400px circle at ${cardTransforms[2].mouseX}px ${cardTransforms[2].mouseY}px, rgba(239, 68, 68, 0.12), transparent 70%)`
          }"
        ></div>

        <!-- Ambient Background Glow -->
        <div class="pointer-events-none absolute -left-20 -top-20 h-64 w-64 rounded-full bg-red-500/10 blur-3xl dark:bg-red-500/20"></div>

        <div>
          <!-- Header Badge & Simulation Toggle -->
          <div class="mb-4 flex flex-wrap items-center justify-between gap-2">
            <span class="inline-flex items-center gap-1.5 rounded-xl border border-red-500/30 bg-red-500/10 px-3 py-1 font-mono text-xs font-semibold text-red-600 dark:text-red-400">
              <span class="h-2 w-2 rounded-full bg-red-500 dark:bg-red-400 animate-pulse"></span>
              Auto-Failover
            </span>
            <button
              @click="triggerFailoverToggle"
              class="flex items-center gap-1.5 rounded-lg border border-gray-200 bg-white/90 px-2.5 py-1 font-mono text-[11px] text-gray-700 shadow-sm transition-all hover:border-red-500/40 hover:bg-gray-100 hover:text-gray-900 dark:border-white/10 dark:bg-white/5 dark:text-gray-300 dark:hover:bg-white/10 dark:hover:text-white"
              title="Click để kiểm tra kịch bản chuyển luồng tự động"
            >
              <span>⚡</span>
              <span>{{ failoverSimActive ? 'Test: 429 Reroute' : 'Test: Healthy' }}</span>
            </button>
          </div>

          <h3 class="text-2xl font-bold tracking-tight text-gray-900 dark:text-white">
            Chuyển Luồng Khẩn Cấp &lt; 30ms
          </h3>
          <p class="mt-2.5 text-sm leading-relaxed text-gray-600 dark:text-dark-300">
            Khi một tài khoản gặp lỗi 429 Too Many Requests hoặc gián đoạn mạng, Gateway tức thì chuyển request sang tài khoản sẵn sàng tiếp theo mà không làm ngắt kết nối lập trình.
          </p>
        </div>

        <!-- Visual Failover Simulator Widget -->
        <div class="relative mt-6 space-y-2.5 rounded-2xl border border-gray-200/80 bg-gray-50/90 p-4 shadow-inner dark:border-white/5 dark:bg-dark-950/80">
          <!-- Primary Account -->
          <div
            :class="[
              'flex items-center justify-between rounded-xl border px-3.5 py-2.5 text-xs transition-all duration-300',
              failoverSimActive
                ? 'border-red-500/30 bg-red-500/10'
                : 'border-emerald-500/30 bg-emerald-500/10'
            ]"
          >
            <div class="flex items-center gap-2">
              <span
                :class="[
                  'flex h-2 w-2 rounded-full',
                  failoverSimActive ? 'bg-red-500' : 'bg-emerald-500 animate-ping'
                ]"
              ></span>
              <span class="font-mono text-gray-800 dark:text-gray-200">Upstream Account #01</span>
            </div>
            <span
              :class="[
                'rounded px-2 py-0.5 font-mono text-[10px] font-bold',
                failoverSimActive ? 'bg-red-500/20 text-red-600 dark:text-red-300' : 'bg-emerald-500/20 text-emerald-600 dark:text-emerald-300'
              ]"
            >
              {{ failoverSimActive ? 'HTTP 429 Rate Limited' : 'HTTP 200 OK • Primary' }}
            </span>
          </div>

          <!-- Reroute Indicator -->
          <div class="flex items-center justify-center gap-2 text-center font-mono text-[11px] text-gray-500 dark:text-gray-400">
            <svg
              :class="[
                'h-3.5 w-3.5 transition-transform duration-300',
                failoverSimActive ? 'text-red-500 dark:text-red-400 animate-bounce' : 'text-emerald-500 dark:text-emerald-400'
              ]"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 14l-7 7m0 0l-7-7m7 7V3" />
            </svg>
            <span :class="failoverSimActive ? 'text-red-600 dark:text-red-300 font-semibold' : 'text-emerald-600 dark:text-emerald-400 font-semibold'">
              {{ failoverSimActive ? 'Seamless Reroute in 18ms' : 'Direct Link Active' }}
            </span>
          </div>

          <!-- Fallback Account -->
          <div
            :class="[
              'flex items-center justify-between rounded-xl border px-3.5 py-2.5 text-xs transition-all duration-300',
              failoverSimActive
                ? 'border-emerald-500/40 bg-emerald-500/15'
                : 'border-gray-200 bg-white dark:border-white/5 dark:bg-white/5'
            ]"
          >
            <div class="flex items-center gap-2">
              <span
                :class="[
                  'flex h-2 w-2 rounded-full',
                  failoverSimActive ? 'bg-emerald-500 animate-ping' : 'bg-gray-400 dark:bg-gray-500'
                ]"
              ></span>
              <span class="font-mono font-semibold text-gray-900 dark:text-white">Upstream Account #04</span>
            </div>
            <span
              :class="[
                'rounded px-2 py-0.5 font-mono text-[10px] font-bold',
                failoverSimActive ? 'bg-emerald-500/20 text-emerald-700 dark:bg-emerald-500/30 dark:text-emerald-300' : 'bg-gray-200 text-gray-600 dark:bg-gray-500/20 dark:text-gray-400'
              ]"
            >
              {{ failoverSimActive ? 'HTTP 200 OK • Serving Backup' : 'Standby • Ready' }}
            </span>
          </div>
        </div>
      </div>

      <!-- ======================================================== -->
      <!-- CARD 3: Token-Level Precise Billing (Col Span 5)         -->
      <!-- ======================================================== -->
      <div
        class="group relative flex flex-col justify-between overflow-hidden rounded-3xl border border-gray-200/80 bg-white/80 p-6 shadow-xl backdrop-blur-xl transition-all duration-300 dark:border-white/10 dark:bg-dark-900/80 lg:col-span-5 sm:p-8"
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
            background: `radial-gradient(400px circle at ${cardTransforms[3].mouseX}px ${cardTransforms[3].mouseY}px, rgba(168, 85, 247, 0.15), transparent 70%)`
          }"
        ></div>

        <!-- Ambient Background Glow -->
        <div class="pointer-events-none absolute -right-20 -bottom-20 h-64 w-64 rounded-full bg-violet-500/10 blur-3xl dark:bg-violet-500/20"></div>

        <div>
          <!-- Header Badge -->
          <div class="mb-4 flex items-center justify-between">
            <span class="inline-flex items-center gap-1.5 rounded-xl border border-violet-500/30 bg-violet-500/10 px-3 py-1 font-mono text-xs font-semibold text-violet-600 dark:text-violet-400">
              <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
              </svg>
              Precise Metering
            </span>
            <span class="font-mono text-xs text-gray-500 dark:text-dark-400">Realtime Quota</span>
          </div>

          <h3 class="text-2xl font-bold tracking-tight text-gray-900 dark:text-white">
            Bóc Tách Chi Phí Token Minh Bạch
          </h3>
          <p class="mt-2.5 text-sm leading-relaxed text-gray-600 dark:text-dark-300">
            Hạch toán chi li từng token: Input, Output, Reasoning và Cached Tokens (chiết khấu tới 80%). Kiểm soát ngân sách nhóm hiệu quả và không chi phí ẩn.
          </p>
        </div>

        <!-- Token Distribution Stack Bar Widget -->
        <div class="relative mt-6 rounded-2xl border border-gray-200/80 bg-gray-50/90 p-4 shadow-inner dark:border-white/5 dark:bg-dark-950/80">
          <!-- Stacked Bar -->
          <div class="mb-3 flex h-4 w-full overflow-hidden rounded-full bg-gray-200 p-0.5 dark:bg-dark-800">
            <div class="h-full rounded-l-full bg-cyan-500 transition-all" style="width: 32%" title="Input Tokens: 32%"></div>
            <div class="h-full bg-emerald-500 transition-all" style="width: 24%" title="Output Tokens: 24%"></div>
            <div class="h-full bg-violet-500 transition-all" style="width: 34%" title="Prompt Cache (Discount 80%): 34%"></div>
            <div class="h-full rounded-r-full bg-amber-500 transition-all" style="width: 10%" title="Reasoning Tokens: 10%"></div>
          </div>

          <!-- Legend Tags -->
          <div class="grid grid-cols-2 gap-2 font-mono text-[11px]">
            <div class="flex items-center gap-1.5 text-gray-700 dark:text-gray-300">
              <span class="h-2 w-2 rounded-full bg-cyan-500"></span>
              <span>Input: $3.00/1M</span>
            </div>
            <div class="flex items-center gap-1.5 text-gray-700 dark:text-gray-300">
              <span class="h-2 w-2 rounded-full bg-emerald-500"></span>
              <span>Output: $15.00/1M</span>
            </div>
            <div class="flex items-center gap-1.5 text-violet-700 dark:text-violet-300 font-semibold">
              <span class="h-2 w-2 rounded-full bg-violet-500"></span>
              <span>Cache: $0.30/1M (-80%)</span>
            </div>
            <div class="flex items-center gap-1.5 text-amber-700 dark:text-amber-300">
              <span class="h-2 w-2 rounded-full bg-amber-500"></span>
              <span>Reasoning: Metered</span>
            </div>
          </div>
        </div>
      </div>

      <!-- ======================================================== -->
      <!-- CARD 4: Enterprise Shield & Secret Redaction (Col Span 7)-->
      <!-- ======================================================== -->
      <div
        class="group relative flex flex-col justify-between overflow-hidden rounded-3xl border border-gray-200/80 bg-white/80 p-6 shadow-xl backdrop-blur-xl transition-all duration-300 dark:border-white/10 dark:bg-dark-900/80 lg:col-span-7 sm:p-8"
        :style="{
          transform: cardTransforms[4].transform,
          transition: cardTransforms[4].isHovered ? 'transform 0.1s ease-out' : 'transform 0.4s ease-out'
        }"
        @mousemove="handleMouseMove($event, 4)"
        @mouseleave="handleMouseLeave(4)"
      >
        <!-- Radial Spotlight Glow -->
        <div
          v-if="cardTransforms[4].isHovered"
          class="pointer-events-none absolute -inset-px rounded-3xl opacity-60 transition-opacity duration-300"
          :style="{
            background: `radial-gradient(450px circle at ${cardTransforms[4].mouseX}px ${cardTransforms[4].mouseY}px, rgba(16, 185, 129, 0.15), transparent 70%)`
          }"
        ></div>

        <!-- Ambient Background Glow -->
        <div class="pointer-events-none absolute -left-20 -bottom-20 h-64 w-64 rounded-full bg-emerald-500/10 blur-3xl dark:bg-emerald-500/20"></div>

        <div>
          <!-- Header Badge -->
          <div class="mb-4 flex items-center justify-between">
            <span class="inline-flex items-center gap-1.5 rounded-xl border border-emerald-500/30 bg-emerald-500/10 px-3 py-1 font-mono text-xs font-semibold text-emerald-600 dark:text-emerald-400">
              <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z" />
              </svg>
              Enterprise Privacy
            </span>
            <span class="font-mono text-xs text-gray-500 dark:text-dark-400">Zero-Log Architecture</span>
          </div>

          <h3 class="text-2xl font-bold tracking-tight text-gray-900 dark:text-white">
            Khiên Bảo Mật &amp; Tự Động Ẩn Khóa Bí Mật
          </h3>
          <p class="mt-2.5 text-sm leading-relaxed text-gray-600 dark:text-dark-300">
            Mọi khóa API, token và thông tin cá nhân (PII) được tự động mã hóa và bôi mờ trong luồng trung gian (Secret Redaction). Cam kết 100% không lưu trữ nội dung prompt nhạy cảm lên đĩa cứng.
          </p>
        </div>

        <!-- Security Redaction Scanner Widget -->
        <div class="relative mt-6 overflow-hidden rounded-2xl border border-gray-200/90 bg-gray-50/95 p-4 font-mono text-xs text-gray-700 shadow-inner dark:border-emerald-500/20 dark:bg-dark-950/80 dark:text-gray-300">
          <div class="mb-2 flex items-center justify-between border-b border-gray-200/80 pb-2 text-[11px] text-gray-500 dark:border-white/5 dark:text-gray-400">
            <span class="flex items-center gap-2">
              <span class="h-2 w-2 rounded-full bg-emerald-500"></span>
              In-Flight Sanitization Filter
            </span>
            <span class="text-emerald-600 dark:text-emerald-400 font-semibold">AES-256 Memory Encrypted</span>
          </div>

          <div class="space-y-1">
            <div class="text-gray-500 dark:text-gray-400">{</div>
            <div class="pl-4">
              <span class="text-cyan-700 dark:text-cyan-300">"upstream_auth":</span>
              <span class="text-emerald-600 dark:text-emerald-400 font-bold"> "Bearer sk-proj-••••••••••••••••"</span>,
            </div>
            <div class="pl-4">
              <span class="text-cyan-700 dark:text-cyan-300">"session_encryption":</span>
              <span class="text-gray-800 dark:text-gray-200"> "ECDH P-256 + ChaCha20"</span>,
            </div>
            <div class="pl-4">
              <span class="text-cyan-700 dark:text-cyan-300">"disk_log_policy":</span>
              <span class="text-emerald-600 dark:text-emerald-400 font-semibold"> "ZERO_PERSISTENCE_ENFORCED"</span>
            </div>
            <div class="text-gray-500 dark:text-gray-400">}</div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
