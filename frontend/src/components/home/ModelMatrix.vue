<script setup lang="ts">
import { ref, computed } from 'vue'

withDefaults(
  defineProps<{
    showModelPlazaEntry?: boolean
  }>(),
  {
    showModelPlazaEntry: false
  }
)

type CategoryType = 'all' | 'thinking' | 'vision' | 'fast'
const activeCategory = ref<CategoryType>('all')
const copiedModelId = ref<string | null>(null)

interface ModelItem {
  id: string
  name: string
  provider: string
  providerBadgeClass: string
  category: 'thinking' | 'vision' | 'fast'
  tags: string[]
  description: string
  context: string
  latency: string
  logoUrl: string
  isMonochrome?: boolean
}

const models: ModelItem[] = [
  {
    id: 'claude-3-7-sonnet',
    name: 'Claude 3.7 Sonnet',
    provider: 'Anthropic',
    providerBadgeClass: 'border-orange-500/30 bg-orange-500/10 text-orange-600 dark:text-orange-400',
    category: 'thinking',
    tags: ['Hybrid Thinking', 'Coding SOTA', '200K Context'],
    description: 'Mô hình đột phá kết hợp suy luận tức thì và chế độ tư duy mở rộng (Thinking Mode) cho lập trình chuyên sâu.',
    context: '200K',
    latency: '~220ms',
    logoUrl: '/models/claude.svg'
  },
  {
    id: 'claude-3-5-haiku',
    name: 'Claude 3.5 Haiku',
    provider: 'Anthropic',
    providerBadgeClass: 'border-orange-500/30 bg-orange-500/10 text-orange-600 dark:text-orange-400',
    category: 'fast',
    tags: ['Ultra Fast', 'Lightweight', 'High Concurrency'],
    description: 'Tốc độ phản hồi cực nhanh với chi phí thấp kỷ lục, thích hợp cho tự động sinh mã và phân tích dữ liệu nhanh.',
    context: '200K',
    latency: '~85ms',
    logoUrl: '/models/claude.svg'
  },
  {
    id: 'o3-mini',
    name: 'OpenAI o3-mini',
    provider: 'OpenAI',
    providerBadgeClass: 'border-emerald-500/30 bg-emerald-500/10 text-emerald-600 dark:text-emerald-400',
    category: 'thinking',
    tags: ['Reasoning', 'STEM & Math', 'Low Latency'],
    description: 'Dòng mô hình suy luận hiệu năng cao, tối ưu hóa cho các bài toán khoa học, giải thuật và phân tích logic phức tạp.',
    context: '128K',
    latency: '~310ms',
    logoUrl: '/models/openai.svg'
  },
  {
    id: 'gemini-2-5-pro',
    name: 'Gemini 2.5 Pro',
    provider: 'Google DeepMind',
    providerBadgeClass: 'border-blue-500/30 bg-blue-500/10 text-blue-600 dark:text-blue-400',
    category: 'vision',
    tags: ['2M Context', 'Video/Audio Native', 'Multimodal'],
    description: 'Cửa sổ ngữ cảnh khổng lồ 2 triệu tokens, xử lý trọn vẹn toàn bộ codebase lớn hoặc tài liệu video kéo dài hàng giờ.',
    context: '2M Tokens',
    latency: '~260ms',
    logoUrl: '/models/gemini.svg'
  },
  {
    id: 'grok-3',
    name: 'Grok 3',
    provider: 'xAI',
    providerBadgeClass: 'border-slate-400/30 bg-slate-500/10 text-slate-700 dark:text-slate-300',
    category: 'vision',
    tags: ['Realtime Data', 'Vision & Audio', 'Colossus Supercluster'],
    description: 'Sức mạnh tính toán từ siêu cụm Colossus, tích hợp dữ liệu thời gian thực và khả năng phân tích hình ảnh/âm thanh sâu sắc.',
    context: '1M Tokens',
    latency: '~240ms',
    logoUrl: '/models/grok.svg',
    isMonochrome: true
  },
  {
    id: 'deepseek-r1',
    name: 'DeepSeek R1',
    provider: 'DeepSeek',
    providerBadgeClass: 'border-cyan-500/30 bg-cyan-500/10 text-cyan-600 dark:text-cyan-400',
    category: 'thinking',
    tags: ['Open Reasoning', 'Transparent CoT', 'Extreme Value'],
    description: 'Mô hình lý luận mã nguồn mở mạnh mẽ nhất, hiển thị minh bạch toàn bộ chuỗi suy nghĩ nội tâm với chi phí tiết kiệm vượt trội.',
    context: '64K',
    latency: '~290ms',
    logoUrl: '/models/deepseek.svg'
  }
]

const filteredModels = computed(() => {
  if (activeCategory.value === 'all') return models
  return models.filter((m) => m.category === activeCategory.value)
})

async function copyModelId(id: string) {
  try {
    await navigator.clipboard.writeText(id)
    copiedModelId.value = id
    setTimeout(() => {
      copiedModelId.value = null
    }, 2000)
  } catch (err) {
    console.error('Failed to copy: ', err)
  }
}
</script>

<template>
  <section class="relative py-8">
    <!-- Header Section -->
    <div class="mb-10 text-center">
      <div class="mb-3 inline-flex items-center gap-2 rounded-full border border-cyan-500/30 bg-cyan-500/10 px-3.5 py-1 text-xs font-semibold text-cyan-400 backdrop-blur-md">
        <span class="h-1.5 w-1.5 rounded-full bg-cyan-400 animate-pulse"></span>
        <span>Hệ Sinh Thái Toàn Diện • Model Ecosystem</span>
      </div>
      <h2 class="text-3xl font-extrabold tracking-tight text-gray-900 dark:text-white sm:text-4xl">
        Kết Nối Mọi Siêu Mô Hình Hàng Đầu
      </h2>
      <p class="mx-auto mt-3 max-w-2xl text-base text-gray-600 dark:text-dark-300">
        Truy cập đồng thời hơn 50+ mô hình AI từ Anthropic, OpenAI, Google DeepMind, xAI và các mô hình mã nguồn mở thế hệ mới chỉ với một API Key duy nhất.
      </p>

      <!-- Category Filter Tabs -->
      <div class="mt-8 flex flex-wrap items-center justify-center gap-2">
        <button
          @click="activeCategory = 'all'"
          :class="[
            'rounded-xl px-4 py-2 font-mono text-xs font-medium transition-all duration-200',
            activeCategory === 'all'
              ? 'bg-gradient-to-r from-cyan-500 to-primary-600 text-white shadow-md shadow-cyan-500/25 ring-1 ring-cyan-400/30'
              : 'border border-gray-200 bg-white/80 text-gray-600 hover:bg-gray-100 hover:text-gray-900 dark:border-white/10 dark:bg-white/5 dark:text-gray-400 dark:hover:bg-white/10 dark:hover:text-white'
          ]"
        >
          Tất cả mô hình (50+)
        </button>
        <button
          @click="activeCategory = 'thinking'"
          :class="[
            'rounded-xl px-4 py-2 font-mono text-xs font-medium transition-all duration-200',
            activeCategory === 'thinking'
              ? 'bg-gradient-to-r from-cyan-500 to-primary-600 text-white shadow-md shadow-cyan-500/25 ring-1 ring-cyan-400/30'
              : 'border border-gray-200 bg-white/80 text-gray-600 hover:bg-gray-100 hover:text-gray-900 dark:border-white/10 dark:bg-white/5 dark:text-gray-400 dark:hover:bg-white/10 dark:hover:text-white'
          ]"
        >
          🧠 Thinking &amp; Suy Luận
        </button>
        <button
          @click="activeCategory = 'vision'"
          :class="[
            'rounded-xl px-4 py-2 font-mono text-xs font-medium transition-all duration-200',
            activeCategory === 'vision'
              ? 'bg-gradient-to-r from-cyan-500 to-primary-600 text-white shadow-md shadow-cyan-500/25 ring-1 ring-cyan-400/30'
              : 'border border-gray-200 bg-white/80 text-gray-600 hover:bg-gray-100 hover:text-gray-900 dark:border-white/10 dark:bg-white/5 dark:text-gray-400 dark:hover:bg-white/10 dark:hover:text-white'
          ]"
        >
          👁️ Đa Phương Tiện &amp; Video
        </button>
        <button
          @click="activeCategory = 'fast'"
          :class="[
            'rounded-xl px-4 py-2 font-mono text-xs font-medium transition-all duration-200',
            activeCategory === 'fast'
              ? 'bg-gradient-to-r from-cyan-500 to-primary-600 text-white shadow-md shadow-cyan-500/25 ring-1 ring-cyan-400/30'
              : 'border border-gray-200 bg-white/80 text-gray-600 hover:bg-gray-100 hover:text-gray-900 dark:border-white/10 dark:bg-white/5 dark:text-gray-400 dark:hover:bg-white/10 dark:hover:text-white'
          ]"
        >
          ⚡ Tốc Độ Siêu Tốc
        </button>
      </div>
    </div>

    <!-- Models Grid (3 Columns on Desktop) -->
    <div class="grid grid-cols-1 gap-6 md:grid-cols-2 lg:grid-cols-3">
      <div
        v-for="model in filteredModels"
        :key="model.id"
        class="group relative flex flex-col justify-between rounded-2xl border border-gray-200/80 bg-white/85 p-6 shadow-lg shadow-gray-200/40 backdrop-blur-xl transition-all duration-300 hover:-translate-y-1 hover:border-cyan-500/50 hover:shadow-xl hover:shadow-cyan-500/10 dark:border-white/10 dark:bg-dark-900/80 dark:shadow-none dark:hover:border-cyan-500/40"
      >
        <!-- Top Row: Icon, Name, Provider, Online Badge -->
        <div>
          <div class="mb-4 flex items-start justify-between">
            <div class="flex items-center gap-3">
              <!-- Model Logo Avatar -->
              <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl border border-gray-100 bg-gray-50/90 p-2 shadow-inner dark:border-white/10 dark:bg-dark-950/80">
                <img
                  :src="model.logoUrl"
                  :alt="model.name"
                  class="h-7 w-7 object-contain transition-transform duration-300 group-hover:scale-110"
                  :class="{ 'dark:invert': model.isMonochrome }"
                />
              </div>
              <div>
                <h3 class="text-base font-bold text-gray-900 transition-colors group-hover:text-cyan-600 dark:text-white dark:group-hover:text-cyan-400">
                  {{ model.name }}
                </h3>
                <span
                  :class="[
                    'inline-block rounded border px-2 py-0.5 text-[10px] font-mono font-medium',
                    model.providerBadgeClass
                  ]"
                >
                  {{ model.provider }}
                </span>
              </div>
            </div>

            <!-- Online Pulse Indicator -->
            <div class="flex items-center gap-1.5 rounded-full border border-emerald-500/30 bg-emerald-500/10 px-2 py-0.5 font-mono text-[10px] text-emerald-500 dark:text-emerald-400">
              <span class="h-1.5 w-1.5 rounded-full bg-emerald-500 dark:bg-emerald-400 animate-ping"></span>
              <span>Online</span>
            </div>
          </div>

          <!-- Description -->
          <p class="mb-4 text-xs leading-relaxed text-gray-600 dark:text-dark-300">
            {{ model.description }}
          </p>

          <!-- Tags -->
          <div class="mb-5 flex flex-wrap gap-1.5">
            <span
              v-for="tag in model.tags"
              :key="tag"
              class="rounded-lg border border-gray-200/80 bg-gray-100/80 px-2 py-0.5 font-mono text-[10px] text-gray-600 dark:border-white/10 dark:bg-dark-950/40 dark:text-dark-300"
            >
              {{ tag }}
            </span>
          </div>
        </div>

        <!-- Bottom Row: Context, Latency & Copy Button -->
        <div class="border-t border-gray-100 pt-4 dark:border-white/5">
          <div class="mb-3 flex items-center justify-between font-mono text-[11px] text-gray-500 dark:text-dark-400">
            <span>Ngữ cảnh: <strong class="font-semibold text-cyan-600 dark:text-cyan-400">{{ model.context }}</strong></span>
            <span>Độ trễ: <strong class="font-semibold text-emerald-600 dark:text-emerald-400">{{ model.latency }}</strong></span>
          </div>

          <!-- Model Identifier Pill with Copy -->
          <button
            @click="copyModelId(model.id)"
            class="group/btn flex w-full items-center justify-between rounded-xl border border-gray-200 bg-gray-50/90 px-3 py-2 font-mono text-xs text-gray-700 transition-all hover:border-cyan-500/50 hover:bg-cyan-50/40 hover:text-cyan-700 active:scale-98 dark:border-white/10 dark:bg-dark-950/60 dark:text-gray-300 dark:hover:border-cyan-500/40 dark:hover:bg-dark-950 dark:hover:text-white"
            :title="'Sao chép định danh model'"
          >
            <span class="truncate text-gray-500 transition-colors group-hover/btn:text-cyan-600 dark:text-gray-400 dark:group-hover/btn:text-cyan-300">{{ model.id }}</span>
            <span class="flex items-center gap-1 text-[11px] text-gray-500 group-hover/btn:text-cyan-600 dark:text-gray-400 dark:group-hover/btn:text-gray-200">
              <svg
                v-if="copiedModelId !== model.id"
                class="h-3.5 w-3.5"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"
                />
              </svg>
              <svg
                v-else
                class="h-3.5 w-3.5 text-emerald-500 dark:text-emerald-400"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
                stroke-width="2.5"
              >
                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
              </svg>
              <span :class="copiedModelId === model.id ? 'font-semibold text-emerald-600 dark:text-emerald-400' : ''">
                {{ copiedModelId === model.id ? 'Đã chép' : 'Copy ID' }}
              </span>
            </span>
          </button>
        </div>
      </div>
    </div>

    <!-- Bottom Model Plaza Link (When Available) -->
    <div v-if="showModelPlazaEntry" class="mt-10 text-center">
      <router-link
        to="/model-plaza"
        class="glass-card inline-flex items-center gap-2 rounded-xl px-6 py-3 font-mono text-xs font-semibold text-cyan-600 transition-all hover:scale-105 hover:border-cyan-500/50 hover:text-cyan-700 dark:text-cyan-300 dark:hover:text-cyan-200"
      >
        <span>Khám phá toàn bộ 50+ Models tại Model Plaza</span>
        <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3" />
        </svg>
      </router-link>
    </div>
  </section>
</template>
