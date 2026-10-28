<script setup lang="ts">
import { ref } from 'vue'

interface ModelBrand {
  id: string
  name: string
  provider: string
  logo: string
  isMonochrome?: boolean
  badge: string
  badgeColor: string
  auraColor: string
  borderColor: string
  description: string
  context: string
}

const copiedId = ref<string | null>(null)

function copyId(id: string) {
  navigator.clipboard.writeText(id)
  copiedId.value = id
  setTimeout(() => {
    copiedId.value = null
  }, 2000)
}

// Row 1 Models for Marquee (Flowing Left)
const row1Models: ModelBrand[] = [
  {
    id: 'claude-3-7-sonnet',
    name: 'Claude 3.7 Sonnet',
    provider: 'Anthropic',
    logo: '/models/claude.svg',
    badge: 'Hybrid Thinking',
    badgeColor: 'border-orange-500/30 text-orange-600 dark:text-orange-400 bg-orange-500/10',
    auraColor: 'hover:shadow-orange-500/20',
    borderColor: 'hover:border-orange-500/50',
    description: 'Tư duy mở rộng & tối ưu code SOTA',
    context: '200K'
  },
  {
    id: 'o3-mini',
    name: 'OpenAI o3-mini',
    provider: 'OpenAI',
    logo: '/models/openai.svg',
    badge: 'Reasoning SOTA',
    badgeColor: 'border-emerald-500/30 text-emerald-600 dark:text-emerald-400 bg-emerald-500/10',
    auraColor: 'hover:shadow-emerald-500/20',
    borderColor: 'hover:border-emerald-500/50',
    description: 'Suy luận toán học & logic cực đại',
    context: '128K'
  },
  {
    id: 'gemini-2-5-pro',
    name: 'Gemini 2.5 Pro',
    provider: 'Google DeepMind',
    logo: '/models/gemini.svg',
    badge: '2M Context',
    badgeColor: 'border-blue-500/30 text-blue-600 dark:text-blue-400 bg-blue-500/10',
    auraColor: 'hover:shadow-blue-500/20',
    borderColor: 'hover:border-blue-500/50',
    description: 'Xử lý video, audio & codebase khổng lồ',
    context: '2M Tokens'
  },
  {
    id: 'deepseek-r1',
    name: 'DeepSeek R1',
    provider: 'DeepSeek',
    logo: '/models/deepseek.svg',
    badge: 'Open Reasoning',
    badgeColor: 'border-cyan-500/30 text-cyan-600 dark:text-cyan-400 bg-cyan-500/10',
    auraColor: 'hover:shadow-cyan-500/20',
    borderColor: 'hover:border-cyan-500/50',
    description: 'Chuỗi suy nghĩ CoT minh bạch, siêu tiết kiệm',
    context: '64K'
  },
  {
    id: 'grok-3',
    name: 'Grok 3',
    provider: 'xAI',
    logo: '/models/grok.svg',
    isMonochrome: true,
    badge: 'Realtime Data',
    badgeColor: 'border-slate-500/30 text-slate-700 dark:text-slate-300 bg-slate-500/10',
    auraColor: 'hover:shadow-slate-500/20',
    borderColor: 'hover:border-slate-500/50',
    description: 'Siêu cụm Colossus & phân tích đa giác quan',
    context: '1M Tokens'
  },
  {
    id: 'claude-3-5-haiku',
    name: 'Claude 3.5 Haiku',
    provider: 'Anthropic',
    logo: '/models/claude.svg',
    badge: 'Ultra Fast',
    badgeColor: 'border-amber-500/30 text-amber-600 dark:text-amber-400 bg-amber-500/10',
    auraColor: 'hover:shadow-amber-500/20',
    borderColor: 'hover:border-amber-500/50',
    description: 'Tốc độ cực nhanh cho các tác vụ thời gian thực',
    context: '200K'
  }
]

// Row 2 Models for Reverse Marquee (Flowing Right)
const row2Models: ModelBrand[] = [
  {
    id: 'llama-3-3-70b',
    name: 'Llama 3.3 70B',
    provider: 'Meta',
    logo: '/models/meta.svg',
    badge: 'Open Weights',
    badgeColor: 'border-indigo-500/30 text-indigo-600 dark:text-indigo-400 bg-indigo-500/10',
    auraColor: 'hover:shadow-indigo-500/20',
    borderColor: 'hover:border-indigo-500/50',
    description: 'Mô hình trọng số mở hiệu năng cấp thương mại',
    context: '128K'
  },
  {
    id: 'mistral-large-2',
    name: 'Mistral Large 2',
    provider: 'Mistral AI',
    logo: '/models/mistral.svg',
    badge: 'Multilingual SOTA',
    badgeColor: 'border-amber-500/30 text-amber-600 dark:text-amber-400 bg-amber-500/10',
    auraColor: 'hover:shadow-amber-500/20',
    borderColor: 'hover:border-amber-500/50',
    description: 'Tiêu chuẩn châu Âu, xử lý đa ngôn ngữ xuất sắc',
    context: '128K'
  },
  {
    id: 'qwen-2-5-coder',
    name: 'Qwen 2.5 Coder',
    provider: 'Alibaba Cloud',
    logo: '/models/qwen.svg',
    badge: 'Code Specialist',
    badgeColor: 'border-purple-500/30 text-purple-600 dark:text-purple-400 bg-purple-500/10',
    auraColor: 'hover:shadow-purple-500/20',
    borderColor: 'hover:border-purple-500/50',
    description: 'Chuyên gia sinh mã & tái cấu trúc hệ thống',
    context: '128K'
  },
  {
    id: 'cohere-command-r-plus',
    name: 'Command R+',
    provider: 'Cohere',
    logo: '/models/cohere.svg',
    badge: 'Enterprise RAG',
    badgeColor: 'border-rose-500/30 text-rose-600 dark:text-rose-400 bg-rose-500/10',
    auraColor: 'hover:shadow-rose-500/20',
    borderColor: 'hover:border-rose-500/50',
    description: 'Tối ưu cho tra cứu & trích dẫn doanh nghiệp',
    context: '128K'
  },
  {
    id: 'perplexity-sonar',
    name: 'Sonar Reasoning',
    provider: 'Perplexity',
    logo: '/models/perplexity.svg',
    badge: 'Live Grounding',
    badgeColor: 'border-teal-500/30 text-teal-600 dark:text-teal-400 bg-teal-500/10',
    auraColor: 'hover:shadow-teal-500/20',
    borderColor: 'hover:border-teal-500/50',
    description: 'Tìm kiếm web & xác thực dữ liệu tức thời',
    context: '128K'
  },
  {
    id: 'deepseek-v3',
    name: 'DeepSeek V3',
    provider: 'DeepSeek',
    logo: '/models/deepseek.svg',
    badge: 'High Throughput',
    badgeColor: 'border-sky-500/30 text-sky-600 dark:text-sky-400 bg-sky-500/10',
    auraColor: 'hover:shadow-sky-500/20',
    borderColor: 'hover:border-sky-500/50',
    description: 'Cân bằng hoàn hảo giữa tốc độ và độ chính xác',
    context: '64K'
  }
]

// Duplicate 4x per set to guarantee seamless infinite loop on any screen resolution (up to 4K)
const set1 = [...row1Models, ...row1Models, ...row1Models]
const row1All = [...set1, ...set1]

const set2 = [...row2Models, ...row2Models, ...row2Models]
const row2All = [...set2, ...set2]

// 4 Featured Spotlight Cards
const spotlightModels = [
  {
    id: 'claude-3-7-sonnet',
    name: 'Claude 3.7 Sonnet',
    provider: 'Anthropic',
    logo: '/models/claude.svg',
    badge: 'Coding SOTA',
    highlight: '80% Prompt Cache Saving',
    accentColor: 'from-orange-500/20 to-amber-500/20',
    borderClass: 'hover:border-orange-500/50',
    badgeClass: 'border-orange-500/30 bg-orange-500/10 text-orange-600 dark:text-orange-400',
    metrics: ['200K Context', 'Thinking Mode', '< 30ms Failover']
  },
  {
    id: 'o3-mini',
    name: 'OpenAI o3-mini',
    provider: 'OpenAI',
    logo: '/models/openai.svg',
    badge: 'Reasoning SOTA',
    highlight: 'Zero-Log Security & Audit',
    accentColor: 'from-emerald-500/20 to-teal-500/20',
    borderClass: 'hover:border-emerald-500/50',
    badgeClass: 'border-emerald-500/30 bg-emerald-500/10 text-emerald-600 dark:text-emerald-400',
    metrics: ['128K Context', 'Math / Logic', 'Function Calling']
  },
  {
    id: 'gemini-2-5-pro',
    name: 'Gemini 2.5 Pro',
    provider: 'Google DeepMind',
    logo: '/models/gemini.svg',
    badge: '2M Tokens',
    highlight: 'Multimodal Video / Audio Native',
    accentColor: 'from-blue-500/20 to-cyan-500/20',
    borderClass: 'hover:border-blue-500/50',
    badgeClass: 'border-blue-500/30 bg-blue-500/10 text-blue-600 dark:text-blue-400',
    metrics: ['2M Context', 'Vision & Audio', 'Full Codebase']
  },
  {
    id: 'deepseek-r1',
    name: 'DeepSeek AI',
    subtitle: 'DeepSeek R1',
    provider: 'DeepSeek AI',
    logo: '/models/deepseek.svg',
    badge: 'Open Weights SOTA',
    highlight: '90% Cost Reduction vs Commercial',
    accentColor: 'from-cyan-500/20 to-indigo-500/20',
    borderClass: 'hover:border-cyan-500/50',
    badgeClass: 'border-cyan-500/30 bg-cyan-500/10 text-cyan-600 dark:text-cyan-400',
    metrics: ['64K CoT', 'Extreme Value', 'Transparent CoT']
  }
]
</script>

<template>
  <section class="relative w-full overflow-hidden">
    <!-- Section Header (Centered in Container) -->
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8 mb-10 text-center">
      <div class="inline-flex items-center gap-2 rounded-full border border-cyan-500/30 bg-cyan-500/10 px-3.5 py-1 text-xs font-semibold text-cyan-600 dark:text-cyan-400">
        <span class="h-2 w-2 rounded-full bg-cyan-400 animate-pulse"></span>
        <span>Hệ Sinh Thái Siêu Mô Hình Đột Phá</span>
      </div>
      <h2 class="mt-3 text-2xl font-black tracking-tight text-gray-900 sm:text-3xl md:text-4xl dark:text-white">
        Tương Thích Toàn Diện Với 50+ Siêu Mô Hình AI
      </h2>
      <p class="mx-auto mt-2.5 max-w-2xl text-sm leading-relaxed text-gray-600 sm:text-base dark:text-dark-300">
        Định tuyến thông minh, tự động chuyển tiếp và bảo lưu cache ngữ cảnh trên các nhà cung cấp AI tiên tiến nhất qua một giao thức duy nhất.
      </p>
    </div>

    <!-- FULL SCREEN SLIDER CONTAINER (Bleeds Edge-to-Edge 100vw across entire screen) -->
    <div class="marquee-fullwidth-wrapper relative w-screen left-1/2 right-1/2 -ml-[50vw] -mr-[50vw] overflow-hidden py-4">
      <!-- Gradient Fade Edge Masks (Fading smoothly into background at screen edges) -->
      <div class="pointer-events-none absolute inset-y-0 left-0 z-20 w-20 sm:w-48 bg-gradient-to-r from-gray-50 via-gray-50/80 to-transparent dark:from-dark-950 dark:via-dark-950/80"></div>
      <div class="pointer-events-none absolute inset-y-0 right-0 z-20 w-20 sm:w-48 bg-gradient-to-l from-gray-50 via-gray-50/80 to-transparent dark:from-dark-950 dark:via-dark-950/80"></div>

      <!-- Row 1: Sliding Left (Flagship Frontier Models) -->
      <div class="overflow-hidden py-2">
        <div class="slider-track-left flex items-center gap-4">
          <div
            v-for="(model, idx) in row1All"
            :key="model.id + '-r1-' + idx"
            class="group/card flex shrink-0 items-center gap-3.5 rounded-2xl border border-gray-200/90 bg-white/95 px-5 py-3.5 shadow-md shadow-cyan-500/5 backdrop-blur-xl transition-all duration-300 hover:scale-105 hover:border-cyan-500/40 hover:shadow-xl dark:border-white/10 dark:bg-dark-900/90 dark:shadow-none dark:hover:border-cyan-500/50"
            :class="[model.borderColor, model.auraColor]"
          >
            <div class="flex h-10 w-10 shrink-0 items-center justify-center rounded-xl border border-gray-100 bg-gray-50 p-1.5 shadow-inner dark:border-white/10 dark:bg-dark-950">
              <img
                :src="model.logo"
                :alt="model.name"
                class="h-7 w-7 object-contain transition-transform duration-300 group-hover/card:scale-110"
                :class="{ 'dark:invert': model.isMonochrome }"
              />
            </div>
            <div>
              <div class="flex items-center gap-2">
                <span class="font-bold text-gray-900 dark:text-white">{{ model.name }}</span>
                <span class="rounded-full border px-2 py-0.5 font-mono text-[10px] font-semibold" :class="model.badgeColor">
                  {{ model.badge }}
                </span>
              </div>
              <div class="flex items-center gap-2 font-mono text-xs text-gray-500 dark:text-dark-400">
                <span>{{ model.provider }}</span>
                <span>&bull;</span>
                <span class="text-emerald-600 dark:text-emerald-400 font-medium">{{ model.context }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Row 2: Sliding Right (Extended & Open Source Models) -->
      <div class="mt-4 overflow-hidden py-2">
        <div class="slider-track-right flex items-center gap-4">
          <div
            v-for="(model, idx) in row2All"
            :key="model.id + '-r2-' + idx"
            class="group/card flex shrink-0 items-center gap-3.5 rounded-2xl border border-gray-200/90 bg-white/95 px-5 py-3.5 shadow-md shadow-purple-500/5 backdrop-blur-xl transition-all duration-300 hover:scale-105 hover:border-purple-500/40 hover:shadow-xl dark:border-white/10 dark:bg-dark-900/90 dark:shadow-none dark:hover:border-purple-500/50"
            :class="[model.borderColor, model.auraColor]"
          >
            <div class="flex h-10 w-10 shrink-0 items-center justify-center rounded-xl border border-gray-100 bg-gray-50 p-1.5 shadow-inner dark:border-white/10 dark:bg-dark-950">
              <img
                :src="model.logo"
                :alt="model.name"
                class="h-7 w-7 object-contain transition-transform duration-300 group-hover/card:scale-110"
                :class="{ 'dark:invert': model.isMonochrome }"
              />
            </div>
            <div>
              <div class="flex items-center gap-2">
                <span class="font-bold text-gray-900 dark:text-white">{{ model.name }}</span>
                <span class="rounded-full border px-2 py-0.5 font-mono text-[10px] font-semibold" :class="model.badgeColor">
                  {{ model.badge }}
                </span>
              </div>
              <div class="flex items-center gap-2 font-mono text-xs text-gray-500 dark:text-dark-400">
                <span>{{ model.provider }}</span>
                <span>&bull;</span>
                <span class="text-emerald-600 dark:text-emerald-400 font-medium">{{ model.context }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- 4 Spotlight Feature Cards (Centered in Container) -->
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8 mt-10 grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4">
      <div
        v-for="spot in spotlightModels"
        :key="spot.id"
        class="group relative flex flex-col justify-between overflow-hidden rounded-2xl border border-gray-200/90 bg-white/80 p-5 shadow-lg backdrop-blur-xl transition-all duration-300 hover:-translate-y-1 hover:shadow-xl dark:border-white/10 dark:bg-dark-900/80"
        :class="spot.borderClass"
      >
        <!-- Ambient Corner Glow -->
        <div
          class="pointer-events-none absolute -right-10 -top-10 h-32 w-32 rounded-full bg-gradient-to-br opacity-50 blur-2xl transition-opacity group-hover:opacity-100"
          :class="spot.accentColor"
        ></div>

        <div>
          <!-- Card Top Row: Logo & Badge -->
          <div class="mb-3.5 flex items-center justify-between">
            <div class="flex h-11 w-11 items-center justify-center rounded-xl border border-gray-100 bg-gray-50 p-2 shadow-inner dark:border-white/10 dark:bg-dark-950">
              <img :src="spot.logo" :alt="spot.name" class="h-7 w-7 object-contain" />
            </div>
            <span class="rounded-full border px-2.5 py-0.5 font-mono text-[10px] font-bold" :class="spot.badgeClass">
              {{ spot.badge }}
            </span>
          </div>

          <!-- Name & Provider -->
          <h3 class="text-base font-bold text-gray-900 dark:text-white">
            {{ spot.name }}
          </h3>
          <p class="font-mono text-xs text-gray-500 dark:text-dark-400">
            {{ spot.provider }}
          </p>

          <!-- Core Feature Highlight -->
          <p class="mt-2.5 text-xs leading-relaxed text-gray-600 dark:text-dark-300">
            {{ spot.highlight }}
          </p>
        </div>

        <!-- Card Bottom Metrics & Copy -->
        <div class="mt-4 border-t border-gray-100 pt-3 dark:border-white/5">
          <div class="mb-2 flex flex-wrap gap-1.5">
            <span
              v-for="m in spot.metrics"
              :key="m"
              class="rounded-md bg-gray-100 px-2 py-0.5 font-mono text-[10px] text-gray-600 dark:bg-white/5 dark:text-dark-300"
            >
              {{ m }}
            </span>
          </div>

          <button
            @click="copyId(spot.id)"
            class="flex w-full items-center justify-center gap-1.5 rounded-lg border border-gray-200 bg-gray-50 py-1.5 font-mono text-xs font-semibold text-gray-700 transition-colors hover:bg-gray-100 dark:border-white/10 dark:bg-white/5 dark:text-gray-300 dark:hover:bg-white/10"
          >
            <span>{{ copiedId === spot.id ? '✓ Đã sao chép ID' : `Copy "${spot.id}"` }}</span>
          </button>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
@keyframes slideTrackLeft {
  0% {
    transform: translate3d(0, 0, 0);
  }
  100% {
    transform: translate3d(-50%, 0, 0);
  }
}

@keyframes slideTrackRight {
  0% {
    transform: translate3d(-50%, 0, 0);
  }
  100% {
    transform: translate3d(0, 0, 0);
  }
}

.slider-track-left {
  display: flex;
  width: max-content;
  animation: slideTrackLeft 55s linear infinite;
  will-change: transform;
}

.slider-track-right {
  display: flex;
  width: max-content;
  animation: slideTrackRight 55s linear infinite;
  will-change: transform;
}

.marquee-fullwidth-wrapper:hover .slider-track-left,
.marquee-fullwidth-wrapper:hover .slider-track-right {
  animation-play-state: paused;
}
</style>
