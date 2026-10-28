<template>
  <div class="auth-sliding-wrapper">
    <!-- Desktop Dual-Panel Sliding Container (>= 768px) -->
    <div
      class="auth-card-container hidden md:block bg-white/95 dark:bg-dark-900/95 text-gray-900 dark:text-white border border-gray-200/80 dark:border-white/10 shadow-2xl backdrop-blur-xl"
      :class="{ 'is-active-register': modelValue }"
    >
      <!-- Form Panel Left: Sign In (Login) -->
      <div class="form-box-panel form-box-signin">
        <div class="form-inner-scroll">
          <slot name="login" />
        </div>
      </div>

      <!-- Form Panel Right: Sign Up (Register) -->
      <div class="form-box-panel form-box-signup">
        <div class="form-inner-scroll">
          <slot name="register" />
        </div>
      </div>

      <!-- Sliding Overlay Curved Panel -->
      <div class="toggle-overlay-container">
        <!-- Glow Backdrops inside overlay -->
        <div class="pointer-events-none absolute inset-0 overflow-hidden">
          <div class="absolute -right-20 -top-20 h-64 w-64 rounded-full bg-emerald-400/25 blur-3xl"></div>
          <div class="absolute -bottom-20 -left-20 h-64 w-64 rounded-full bg-teal-300/20 blur-3xl"></div>
          <div class="absolute inset-0 bg-[radial-gradient(#ffffff15_1px,transparent_1px)] [background-size:20px_20px] opacity-40"></div>
        </div>

        <!-- Overlay Content Left: Shown when isRegister = true (Invites to Login) -->
        <div class="toggle-content-box toggle-content-left">
          <div class="mb-4 inline-flex h-16 w-16 items-center justify-center rounded-2xl bg-white/10 p-3 shadow-inner backdrop-blur-md ring-1 ring-white/20">
            <slot name="logo">
              <img src="/logo.svg" alt="Sub2API Logo" class="h-full w-full object-contain drop-shadow" />
            </slot>
          </div>
          <h2 class="text-3xl font-extrabold tracking-tight text-white drop-shadow-sm">
            {{ welcomeBackTitle }}
          </h2>
          <p class="mt-3 max-w-xs text-sm leading-relaxed text-teal-100/90">
            {{ welcomeBackSubtitle }}
          </p>
          <button
            type="button"
            class="toggle-ghost-btn group mt-8"
            @click="emitToggle(false)"
          >
            <span>{{ loginButtonText }}</span>
            <svg class="h-4 w-4 transition-transform duration-300 group-hover:-translate-x-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
            </svg>
          </button>
        </div>

        <!-- Overlay Content Right: Shown when isRegister = false (Invites to Register) -->
        <div class="toggle-content-box toggle-content-right">
          <div class="mb-4 inline-flex h-16 w-16 items-center justify-center rounded-2xl bg-white/10 p-3 shadow-inner backdrop-blur-md ring-1 ring-white/20">
            <slot name="logo">
              <img src="/logo.svg" alt="Sub2API Logo" class="h-full w-full object-contain drop-shadow" />
            </slot>
          </div>
          <h2 class="text-3xl font-extrabold tracking-tight text-white drop-shadow-sm">
            {{ newHereTitle }}
          </h2>
          <p class="mt-3 max-w-xs text-sm leading-relaxed text-teal-100/90">
            {{ newHereSubtitle }}
          </p>
          <button
            type="button"
            class="toggle-ghost-btn group mt-8"
            @click="emitToggle(true)"
          >
            <span>{{ registerButtonText }}</span>
            <svg class="h-4 w-4 transition-transform duration-300 group-hover:translate-x-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3" />
            </svg>
          </button>
        </div>
      </div>
    </div>

    <!-- Mobile Stacked / Tab Switcher Layout (< 768px) -->
    <div class="mobile-auth-container block md:hidden">
      <!-- Mobile Segmented Tab Switcher -->
      <div class="mb-6 flex rounded-2xl bg-gray-100/80 p-1.5 backdrop-blur-md dark:bg-dark-800/80">
        <button
          type="button"
          class="relative flex-1 py-2.5 text-center text-sm font-semibold transition-colors duration-200"
          :class="!modelValue ? 'text-primary-600 dark:text-primary-400' : 'text-gray-500 hover:text-gray-700 dark:text-dark-400'"
          @click="emitToggle(false)"
        >
          <span
            v-if="!modelValue"
            class="absolute inset-0 rounded-xl bg-white shadow-sm dark:bg-dark-700"
          ></span>
          <span class="relative z-10">{{ loginTabLabel }}</span>
        </button>
        <button
          type="button"
          class="relative flex-1 py-2.5 text-center text-sm font-semibold transition-colors duration-200"
          :class="modelValue ? 'text-primary-600 dark:text-primary-400' : 'text-gray-500 hover:text-gray-700 dark:text-dark-400'"
          @click="emitToggle(true)"
        >
          <span
            v-if="modelValue"
            class="absolute inset-0 rounded-xl bg-white shadow-sm dark:bg-dark-700"
          ></span>
          <span class="relative z-10">{{ registerTabLabel }}</span>
        </button>
      </div>

      <!-- Mobile Active Form Panel -->
      <div class="card-glass rounded-2xl p-6 shadow-glass">
        <div v-show="!modelValue" class="animate-fadeIn">
          <slot name="login" />
        </div>
        <div v-show="modelValue" class="animate-fadeIn">
          <slot name="register" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  modelValue: boolean // true = register, false = login
  welcomeBackTitle?: string
  welcomeBackSubtitle?: string
  newHereTitle?: string
  newHereSubtitle?: string
  loginButtonText?: string
  registerButtonText?: string
  loginTabLabel?: string
  registerTabLabel?: string
}

withDefaults(defineProps<Props>(), {
  welcomeBackTitle: 'Welcome Back!',
  welcomeBackSubtitle: 'Already have an account? Sign in to access your API keys and services.',
  newHereTitle: 'Hello, Friend!',
  newHereSubtitle: 'Enter your details and start your journey with Sub2API gateway today.',
  loginButtonText: 'Sign In',
  registerButtonText: 'Create Account',
  loginTabLabel: 'Sign In',
  registerTabLabel: 'Sign Up'
})

const emit = defineEmits<{
  (e: 'update:modelValue', value: boolean): void
  (e: 'change', value: 'login' | 'register'): void
}>()

function emitToggle(isRegister: boolean) {
  emit('update:modelValue', isRegister)
  emit('change', isRegister ? 'register' : 'login')
}
</script>

<style scoped>
/* Main Wrapper */
.auth-sliding-wrapper {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Container dimensions & Glass styling */
.auth-card-container {
  position: relative;
  width: 100%;
  max-width: 960px;
  min-height: 660px;
  border-radius: 32px;
  overflow: hidden;
}

/* Form Box Panels (Left & Right halves) */
.form-box-panel {
  position: absolute;
  top: 0;
  width: 50%;
  height: 100%;
  display: flex;
  flex-direction: column;
  transition: transform 0.65s cubic-bezier(0.65, 0, 0.35, 1),
              opacity 0.4s cubic-bezier(0.65, 0, 0.35, 1),
              visibility 0.65s;
  padding: 40px 48px;
  box-sizing: border-box;
}

.form-inner-scroll {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  overflow-y: auto;
  scrollbar-width: thin;
  scrollbar-color: rgba(20, 184, 166, 0.3) transparent;
  padding-right: 4px;
}

.form-inner-scroll::-webkit-scrollbar {
  width: 5px;
}

.form-inner-scroll::-webkit-scrollbar-thumb {
  background-color: rgba(20, 184, 166, 0.3);
  border-radius: 9999px;
}

/* Default (Login Mode) */
.form-box-signin {
  left: 0;
  z-index: 20;
  opacity: 1;
  visibility: visible;
  transform: translateX(0);
}

.form-box-signup {
  right: 0;
  z-index: 10;
  opacity: 0;
  visibility: hidden;
  pointer-events: none;
  transform: translateX(-20%);
}

/* Active (Register Mode) */
.auth-card-container.is-active-register .form-box-signin {
  transform: translateX(100%);
  opacity: 0;
  visibility: hidden;
  pointer-events: none;
  z-index: 10;
}

.auth-card-container.is-active-register .form-box-signup {
  transform: translateX(0);
  opacity: 1;
  visibility: visible;
  pointer-events: auto;
  z-index: 20;
}

/* Sliding Overlay Container (With organic curved morph) */
.toggle-overlay-container {
  position: absolute;
  top: 0;
  left: 50%;
  width: 50%;
  height: 100%;
  overflow: hidden;
  z-index: 30;
  background: linear-gradient(135deg, #0d9488 0%, #0f766e 45%, #042f2e 100%);
  transition: transform 0.65s cubic-bezier(0.65, 0, 0.35, 1),
              border-radius 0.65s cubic-bezier(0.65, 0, 0.35, 1);
  /* Organic curved edge facing left when positioned on right side */
  border-radius: 160px 0 0 100px;
  box-shadow: 0 0 35px rgba(13, 148, 136, 0.35);
}

/* When Register is active, slide overlay to left & invert the curve */
.auth-card-container.is-active-register .toggle-overlay-container {
  transform: translateX(-100%);
  border-radius: 0 160px 100px 0;
}

/* Overlay Content Boxes */
.toggle-content-box {
  position: absolute;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px;
  text-align: center;
  color: #ffffff;
  box-sizing: border-box;
  transition: transform 0.65s cubic-bezier(0.65, 0, 0.35, 1),
              opacity 0.4s ease-in-out;
}

/* Left overlay content (shown when isRegister is true) */
.toggle-content-left {
  left: 0;
  transform: translateX(-200%);
  opacity: 0;
  pointer-events: none;
}

.auth-card-container.is-active-register .toggle-content-left {
  transform: translateX(0);
  opacity: 1;
  pointer-events: auto;
}

/* Right overlay content (shown when isRegister is false) */
.toggle-content-right {
  right: 0;
  transform: translateX(0);
  opacity: 1;
  pointer-events: auto;
}

.auth-card-container.is-active-register .toggle-content-right {
  transform: translateX(200%);
  opacity: 0;
  pointer-events: none;
}

/* Ghost Pill Button on Overlay */
.toggle-ghost-btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 12px 32px;
  border-radius: 9999px;
  border: 1.5px solid rgba(255, 255, 255, 0.85);
  background: rgba(255, 255, 255, 0.08);
  color: #ffffff;
  font-weight: 600;
  font-size: 0.925rem;
  letter-spacing: 0.025em;
  backdrop-filter: blur(8px);
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

.toggle-ghost-btn:hover {
  background: #ffffff;
  color: #0f766e;
  border-color: #ffffff;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2), 0 0 20px rgba(255, 255, 255, 0.4);
  transform: translateY(-2px);
}

.toggle-ghost-btn:active {
  transform: translateY(0);
}

/* Mobile Container */
.mobile-auth-container {
  width: 100%;
  max-width: 440px;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(6px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fadeIn {
  animation: fadeIn 0.3s ease-out forwards;
}
</style>
