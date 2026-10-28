<script setup lang="ts">
import { ref, computed } from 'vue'

const props = withDefaults(
  defineProps<{
    apiBaseUrl?: string
  }>(),
  {
    apiBaseUrl: ''
  }
)

const effectiveBaseUrl = computed(() => {
  if (props.apiBaseUrl && props.apiBaseUrl.trim()) {
    return props.apiBaseUrl.replace(/\/+$/, '')
  }
  if (typeof window !== 'undefined') {
    return window.location.origin
  }
  return 'http://localhost:8080'
})

type TabType = 'claude' | 'cursor' | 'python' | 'curl'
const activeTab = ref<TabType>('claude')
const copied = ref(false)
const isSimulating = ref(false)
const simulationLines = ref<string[]>([])
const simulationDone = ref(false)

interface TabContent {
  id: TabType
  label: string
  icon: string
  badge: string
  badgeColor: string
  code: string
  description: string
}

const tabs = computed<TabContent[]>(() => [
  {
    id: 'claude',
    label: 'Claude Code CLI',
    icon: '⚡',
    badge: 'Pro / Team Quota',
    badgeColor: 'border-orange-500/30 text-orange-400 bg-orange-500/10',
    description: 'Chạy trực tiếp công cụ lập trình Claude Code mà không lo gián đoạn quota.',
    code: `# 1. Cấu hình biến môi trường kết nối Sub2API
export ANTHROPIC_BASE_URL="${effectiveBaseUrl.value}/v1"
export ANTHROPIC_AUTH_TOKEN="sk-sub2api-your-key"

# 2. Bắt đầu làm việc với Claude 3.7 Sonnet Thinking
claude -p "Phân tích và tối ưu hóa hiệu năng hàm này cho tôi"`
  },
  {
    id: 'cursor',
    label: 'Cursor / Cline',
    icon: '💻',
    badge: 'Dual Protocol',
    badgeColor: 'border-cyan-500/30 text-cyan-400 bg-cyan-500/10',
    description: 'Tương thích 100% với Cursor IDE, Cline, Roo Code, Continue.dev.',
    code: `// Cấu hình trong Settings -> Models -> OpenAI / Anthropic:
{
  "api_endpoint": "${effectiveBaseUrl.value}/v1",
  "api_key": "sk-sub2api-your-key",
  "model_name": "claude-3-7-sonnet",
  "supports_prompt_caching": true,
  "sticky_session": "enabled"
}`
  },
  {
    id: 'python',
    label: 'OpenAI SDK (Python)',
    icon: '🐍',
    badge: 'Fast Stream',
    badgeColor: 'border-emerald-500/30 text-emerald-400 bg-emerald-500/10',
    description: 'Dùng trực tiếp thư viện openai chính thức, chỉ cần thay thế base_url.',
    code: `from openai import OpenAI

# Tự động định tuyến sang Claude / GPT / Gemini
client = OpenAI(
    base_url="${effectiveBaseUrl.value}/v1",
    api_key="sk-sub2api-your-key"
)

stream = client.chat.completions.create(
    model="claude-3-7-sonnet",
    messages=[{"role": "user", "content": "Xin chào Sub2API!"}],
    stream=True
)

for chunk in stream:
    print(chunk.choices[0].delta.content or "", end="")`
  },
  {
    id: 'curl',
    label: 'cURL / REST API',
    icon: '🌐',
    badge: 'Universal',
    badgeColor: 'border-violet-500/30 text-violet-400 bg-violet-500/10',
    description: 'Tương thích cả 2 chuẩn /v1/messages (Anthropic) và /v1/chat/completions (OpenAI).',
    code: `curl -X POST "${effectiveBaseUrl.value}/v1/messages" \\
  -H "Authorization: Bearer sk-sub2api-your-key" \\
  -H "anthropic-version: 2023-06-01" \\
  -H "Content-Type: application/json" \\
  -d '{
    "model": "claude-3-7-sonnet",
    "max_tokens": 1024,
    "stream": true,
    "messages": [
      {"role": "user", "content": "Test Gateway Routing"}
    ]
  }'`
  }
])

const currentSnippet = computed(() => {
  const current = tabs.value.find((t) => t.id === activeTab.value)
  return current ? current.code : ''
})

async function copyCode() {
  try {
    await navigator.clipboard.writeText(currentSnippet.value)
    copied.value = true
    setTimeout(() => {
      copied.value = false
    }, 2000)
  } catch (err) {
    console.error('Failed to copy: ', err)
  }
}

// Live Request Simulation
function runSimulation() {
  if (isSimulating.value) return
  isSimulating.value = true
  simulationDone.value = false
  simulationLines.value = []

  const mockSteps = [
    `> [SYN] Gửi request đến ${effectiveBaseUrl.value}/v1/messages...`,
    `> [RESOLVE] Kiểm tra auth key 'sk-sub2api...': Hợp lệ (Group: VIP, Balance: $48.50)`,
    `> [ROUTER] Sticky Session Active -> Ánh xạ vào Anthropic Upstream Account #2`,
    `> [STATUS] HTTP/1.1 200 OK • TTFT (Time-to-first-token): 24ms`,
    `data: {"model":"claude-3-7-sonnet","delta":{"text":"Chào bạn! "}}`,
    `data: {"delta":{"text":"Tôi là mô hình AI được kết nối qua Sub2API Gateway."}}`,
    `data: {"delta":{"text":" Ngữ cảnh hội thoại 1M token được lưu cache an toàn."}}`,
    `data: [DONE]`,
    `✔ [COMPLETED] 148 tokens • Prompt Cache Hit: 88% (Tiết kiệm $0.0034) • Failover: Ready`
  ]

  let stepIdx = 0
  const interval = setInterval(() => {
    if (stepIdx < mockSteps.length) {
      simulationLines.value.push(mockSteps[stepIdx])
      stepIdx++
    } else {
      clearInterval(interval)
      isSimulating.value = false
      simulationDone.value = true
    }
  }, 160)
}

function clearSimulation() {
  simulationLines.value = []
  simulationDone.value = false
  isSimulating.value = false
}
</script>

<template>
  <div class="relative mx-auto w-full max-w-5xl">
    <!-- Ambient Aura Glow Behind Sandbox -->
    <div class="pointer-events-none absolute -inset-1 rounded-3xl bg-gradient-to-r from-cyan-500/20 via-violet-500/20 to-emerald-500/20 opacity-70 blur-xl transition-all duration-700"></div>

    <!-- Terminal Window Container -->
    <div class="relative overflow-hidden rounded-2xl border border-white/10 bg-dark-950/90 shadow-2xl backdrop-blur-2xl">
      <!-- Terminal Window Bar -->
      <div class="flex flex-wrap items-center justify-between gap-3 border-b border-white/10 bg-dark-900/80 px-4 py-3 sm:px-6">
        <!-- Window Controls & Title -->
        <div class="flex items-center gap-3">
          <div class="flex items-center gap-1.5">
            <span class="h-3 w-3 rounded-full bg-red-500/80 transition-opacity hover:opacity-100"></span>
            <span class="h-3 w-3 rounded-full bg-yellow-500/80 transition-opacity hover:opacity-100"></span>
            <span class="h-3 w-3 rounded-full bg-green-500/80 transition-opacity hover:opacity-100"></span>
          </div>
          <div class="flex items-center gap-2 pl-2">
            <span class="font-mono text-xs font-semibold text-gray-300">sub2api-gateway</span>
            <span class="hidden rounded-full border border-cyan-500/30 bg-cyan-500/10 px-2 py-0.5 font-mono text-[10px] text-cyan-300 sm:inline-block">
              HTTP/2 & WebSocket
            </span>
          </div>
        </div>

        <!-- Action Buttons: Simulate & Copy -->
        <div class="flex items-center gap-2">
          <!-- Run Simulation Button -->
          <button
            @click="runSimulation"
            :disabled="isSimulating"
            class="flex items-center gap-1.5 rounded-lg border border-primary-500/40 bg-primary-500/10 px-3 py-1.5 font-mono text-xs font-medium text-primary-300 transition-all hover:bg-primary-500/20 hover:shadow-sm active:scale-95 disabled:opacity-50"
            :title="'Gửi request mô phỏng thời gian thực'"
          >
            <svg
              v-if="!isSimulating"
              class="h-3.5 w-3.5 text-primary-400"
              fill="currentColor"
              viewBox="0 0 24 24"
            >
              <path d="M8 5v14l11-7z" />
            </svg>
            <svg
              v-else
              class="h-3.5 w-3.5 animate-spin text-primary-400"
              fill="none"
              viewBox="0 0 24 24"
            >
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v8H4z"></path>
            </svg>
            <span>{{ isSimulating ? 'Đang gửi...' : 'Mô phỏng Request' }}</span>
          </button>

          <!-- Copy Button -->
          <button
            @click="copyCode"
            class="flex items-center gap-1.5 rounded-lg border border-white/10 bg-white/5 px-3 py-1.5 font-mono text-xs font-medium text-gray-300 transition-all hover:bg-white/10 hover:text-white active:scale-95"
            :title="'Sao chép đoạn mã'"
          >
            <svg
              v-if="!copied"
              class="h-3.5 w-3.5 text-gray-400"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
              stroke-width="2"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"
              />
            </svg>
            <svg
              v-else
              class="h-3.5 w-3.5 text-emerald-400"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
              stroke-width="2.5"
            >
              <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
            </svg>
            <span>{{ copied ? 'Đã chép!' : 'Copy' }}</span>
          </button>
        </div>
      </div>

      <!-- Tool Tabs Navigation -->
      <div class="flex overflow-x-auto border-b border-white/5 bg-dark-950/60 px-4 py-2 scrollbar-hide">
        <div class="flex items-center gap-1.5">
          <button
            v-for="tab in tabs"
            :key="tab.id"
            @click="activeTab = tab.id"
            :class="[
              'group relative flex items-center gap-2 rounded-xl px-3.5 py-2 font-mono text-xs font-medium transition-all duration-200',
              activeTab === tab.id
                ? 'bg-white/10 text-white shadow-sm'
                : 'text-gray-400 hover:bg-white/5 hover:text-gray-200'
            ]"
          >
            <span>{{ tab.icon }}</span>
            <span>{{ tab.label }}</span>
            <span
              :class="[
                'hidden rounded-full border px-1.5 py-0.2 text-[9px] sm:inline-block',
                tab.badgeColor
              ]"
            >
              {{ tab.badge }}
            </span>
          </button>
        </div>
      </div>

      <!-- Code Content View -->
      <div class="p-4 sm:p-6">
        <!-- Tool Description Banner -->
        <div class="mb-4 flex items-center justify-between text-xs text-gray-400">
          <div class="flex items-center gap-2">
            <span class="inline-block h-1.5 w-1.5 rounded-full bg-cyan-400"></span>
            <span>{{ tabs.find((t) => t.id === activeTab)?.description }}</span>
          </div>
          <span class="font-mono text-[11px] text-gray-500">utf-8 • bash</span>
        </div>

        <!-- Code Block with Syntax Styling -->
        <pre class="overflow-x-auto rounded-xl border border-white/5 bg-black/50 p-4 font-mono text-xs leading-relaxed text-gray-200 sm:text-sm"><code>{{ currentSnippet }}</code></pre>

        <!-- Live Simulation Output Panel (When Triggered) -->
        <div
          v-if="simulationLines.length > 0"
          class="mt-4 overflow-hidden rounded-xl border border-cyan-500/20 bg-dark-900/90 shadow-inner"
        >
          <div class="flex items-center justify-between border-b border-white/5 bg-cyan-950/30 px-4 py-2 text-xs">
            <div class="flex items-center gap-2 text-cyan-300">
              <span class="relative flex h-2 w-2">
                <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-cyan-400 opacity-75"></span>
                <span class="relative inline-flex h-2 w-2 rounded-full bg-cyan-500"></span>
              </span>
              <span class="font-mono font-semibold">Live Traffic Simulation Output</span>
            </div>
            <button
              @click="clearSimulation"
              class="font-mono text-[11px] text-gray-400 transition-colors hover:text-white"
            >
              Clear
            </button>
          </div>
          <div class="space-y-1 p-4 font-mono text-xs">
            <div
              v-for="(line, idx) in simulationLines"
              :key="idx"
              :class="[
                'leading-relaxed transition-opacity duration-200',
                line.startsWith('>') ? 'text-gray-400' : '',
                line.startsWith('data:') ? 'text-cyan-300' : '',
                line.startsWith('✔') ? 'font-semibold text-emerald-400' : 'text-gray-300'
              ]"
            >
              {{ line }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
