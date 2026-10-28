<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, watch } from 'vue'
import * as THREE from 'three'

interface ProviderInfo {
  id: string
  name: string
  subtitle: string
  color: number
  colorHex: string
  position: [number, number, number]
  logoUrl: string
  isMonochrome?: boolean
  feature: string
  badge: string
  latency: string
  context: string
}

const props = withDefaults(
  defineProps<{
    activeProvider?: string
  }>(),
  {
    activeProvider: 'all'
  }
)

const containerRef = ref<HTMLDivElement | null>(null)
let renderer: THREE.WebGLRenderer | null = null
let scene: THREE.Scene | null = null
let camera: THREE.PerspectiveCamera | null = null
let animationFrameId: number | null = null
let resizeObserver: ResizeObserver | null = null
let intersectionObserver: IntersectionObserver | null = null
let isVisible = true

// 3D Objects references
let rootGroup: THREE.Group | null = null
let innerCore: THREE.Mesh | null = null
let innerGlowSphere: THREE.Mesh | null = null
let outerLattice: THREE.Mesh | null = null
let particlesMesh: THREE.Points | null = null

// Satellites & Beams
interface SatelliteRef {
  id: string
  info: ProviderInfo
  sprite: THREE.Sprite
  hitMesh: THREE.Mesh
  beam: THREE.Line
  halo: THREE.Mesh
  photons: THREE.Mesh[]
  originalColor: number
  targetPos: THREE.Vector3
}
const satellites: SatelliteRef[] = []
const hitMeshes: THREE.Mesh[] = []

// 6 Premier AI Models Orbiting in 3D
const PROVIDERS: ProviderInfo[] = [
  {
    id: 'claude',
    name: 'Anthropic Claude',
    subtitle: 'Claude 3.7 Sonnet',
    color: 0xf97316,
    colorHex: '#f97316',
    position: [3.5, 1.8, 0.8],
    logoUrl: '/models/claude.svg',
    feature: 'Hybrid Thinking • 80% Cache Saving',
    badge: 'Coding SOTA',
    latency: '18ms',
    context: '200K'
  },
  {
    id: 'openai',
    name: 'OpenAI',
    subtitle: 'o3-mini & GPT-4o',
    color: 0x10b981,
    colorHex: '#10b981',
    position: [-3.7, 1.2, 1.0],
    logoUrl: '/models/openai.svg',
    feature: 'Reasoning SOTA • Zero-Log Security',
    badge: 'Logic & Math',
    latency: '24ms',
    context: '128K'
  },
  {
    id: 'gemini',
    name: 'Google Gemini',
    subtitle: 'Gemini 2.5 Pro',
    color: 0x3b82f6,
    colorHex: '#3b82f6',
    position: [3.3, -2.0, 1.0],
    logoUrl: '/models/gemini.svg',
    feature: '2M Tokens • Video & Code Native',
    badge: 'Multimodal',
    latency: '26ms',
    context: '2M Tokens'
  },
  {
    id: 'deepseek',
    name: 'DeepSeek AI',
    subtitle: 'DeepSeek R1',
    color: 0x06b6d4,
    colorHex: '#06b6d4',
    position: [-3.4, -2.0, -0.6],
    logoUrl: '/models/deepseek.svg',
    feature: 'Transparent CoT • -90% Cost Saving',
    badge: 'Open Reasoning',
    latency: '28ms',
    context: '64K'
  },
  {
    id: 'grok',
    name: 'xAI Grok',
    subtitle: 'Grok 3 Colossus',
    color: 0xe2e8f0,
    colorHex: '#cbd5e1',
    position: [-0.8, 3.3, -1.2],
    logoUrl: '/models/grok.svg',
    isMonochrome: true,
    feature: 'Realtime Data • 100K RPS Capacity',
    badge: 'Realtime Compute',
    latency: '22ms',
    context: '1M Tokens'
  },
  {
    id: 'meta',
    name: 'Meta Llama',
    subtitle: 'Llama 3.3 70B',
    color: 0x8b5cf6,
    colorHex: '#8b5cf6',
    position: [0.5, -3.5, 1.2],
    logoUrl: '/models/meta.svg',
    feature: 'Open Weights SOTA • High Concurrency',
    badge: 'Open Weights',
    latency: '20ms',
    context: '128K'
  }
]

// Mouse interaction & Raycasting
let mouseX = 0
let mouseY = 0
let targetRotationX = 0
let targetRotationY = 0
let currentRotationX = 0
let currentRotationY = 0
let isHovering = false

// Cached vectors to avoid GC pressure in 60fps render loop
const zeroVector = new THREE.Vector3(0, 0, 0)
const tempWorldPos = new THREE.Vector3()
let containerRect: DOMRect | null = null
let autoRotationY = 0

// Raycaster for 3D Satellite Hover
const raycaster = new THREE.Raycaster()
const mouseVector = new THREE.Vector2()
const hoveredSatellite = ref<ProviderInfo | null>(null)
const selectedProviderId = ref<string>('all')
const tooltipPos = ref<{ x: number; y: number }>({ x: 0, y: 0 })

function selectProvider(id: string) {
  selectedProviderId.value = selectedProviderId.value === id ? 'all' : id
}

function handleMouseMove(e: MouseEvent) {
  if (!containerRef.value) return
  if (!containerRect) {
    containerRect = containerRef.value.getBoundingClientRect()
  }
  mouseX = ((e.clientX - containerRect.left) / containerRect.width) * 2 - 1
  mouseY = -(((e.clientY - containerRect.top) / containerRect.height) * 2 - 1)
  
  // Smooth target rotation (reduced sensitivity for silky feel)
  targetRotationY = mouseX * 0.35
  targetRotationX = -mouseY * 0.22

  mouseVector.x = mouseX
  mouseVector.y = mouseY
}

function handleMouseEnter() {
  isHovering = true
  if (containerRef.value) {
    containerRect = containerRef.value.getBoundingClientRect()
  }
}

function handleMouseLeave() {
  isHovering = false
  hoveredSatellite.value = null
  targetRotationX = 0
  targetRotationY = 0
}

// Helper: Create 3D Holographic Canvas Badge with Official Model SVG
function createHolographicBadgeTexture(info: ProviderInfo): THREE.CanvasTexture {
  const canvas = document.createElement('canvas')
  canvas.width = 256
  canvas.height = 256
  const ctx = canvas.getContext('2d')!

  const texture = new THREE.CanvasTexture(canvas)
  texture.minFilter = THREE.LinearFilter
  texture.magFilter = THREE.LinearFilter

  function renderBadge(loadedImage?: HTMLImageElement) {
    ctx.clearRect(0, 0, 256, 256)

    // Outer Glow Circle
    const glowGrad = ctx.createRadialGradient(128, 128, 80, 128, 128, 124)
    glowGrad.addColorStop(0, 'rgba(15, 23, 42, 0.92)')
    glowGrad.addColorStop(0.85, 'rgba(15, 23, 42, 0.96)')
    glowGrad.addColorStop(1, info.colorHex)

    ctx.save()
    ctx.beginPath()
    ctx.arc(128, 128, 116, 0, Math.PI * 2)
    ctx.fillStyle = glowGrad
    ctx.fill()

    // Glowing Hologram Border
    ctx.lineWidth = 6
    ctx.strokeStyle = info.colorHex
    ctx.shadowColor = info.colorHex
    ctx.shadowBlur = 18
    ctx.stroke()
    ctx.restore()

    // Inner Decorative Tech Ring
    ctx.save()
    ctx.beginPath()
    ctx.arc(128, 128, 98, 0, Math.PI * 2)
    ctx.lineWidth = 1.5
    ctx.strokeStyle = 'rgba(255, 255, 255, 0.25)'
    ctx.setLineDash([6, 6])
    ctx.stroke()
    ctx.restore()

    // Draw Official Model SVG Logo centered
    if (loadedImage) {
      ctx.save()
      if (info.isMonochrome) {
        // Boost contrast for monochrome icons in dark circular badge
        ctx.filter = 'brightness(2.2)'
      }
      const iconSize = 120
      const offset = (256 - iconSize) / 2
      ctx.drawImage(loadedImage, offset, offset, iconSize, iconSize)
      ctx.restore()
    }

    texture.needsUpdate = true
  }

  // Initial render (badge base)
  renderBadge()

  // Load official model SVG and redraw onto badge
  const img = new Image()
  img.crossOrigin = 'anonymous'
  img.src = info.logoUrl
  img.onload = () => {
    renderBadge(img)
  }

  return texture
}

function initThree() {
  if (!containerRef.value) return

  const width = containerRef.value.clientWidth
  const height = containerRef.value.clientHeight || 450

  // 1. Scene
  scene = new THREE.Scene()

  // 2. Camera
  camera = new THREE.PerspectiveCamera(45, width / height, 0.1, 1000)
  camera.position.z = 12.2

  // 3. Renderer with high performance & transparent background
  renderer = new THREE.WebGLRenderer({
    antialias: true,
    alpha: true,
    powerPreference: 'high-performance'
  })
  renderer.setSize(width, height)
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
  renderer.toneMapping = THREE.ACESFilmicToneMapping
  renderer.toneMappingExposure = 1.2
  containerRef.value.appendChild(renderer.domElement)

  // 4. Root container for smooth rotation
  rootGroup = new THREE.Group()
  scene.add(rootGroup)

  // 5. Lights
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.8)
  scene.add(ambientLight)

  const centralPointLight = new THREE.PointLight(0x06b6d4, 3, 20)
  centralPointLight.position.set(0, 0, 0)
  rootGroup.add(centralPointLight)

  // 6. Central Quantum Gateway Core (Icosahedron Solid)
  const coreGeo = new THREE.IcosahedronGeometry(1.6, 2)
  const coreMat = new THREE.MeshPhongMaterial({
    color: 0x06b6d4,
    emissive: 0x0891b2,
    emissiveIntensity: 0.65,
    shininess: 90,
    transparent: true,
    opacity: 0.85,
    wireframe: false
  })
  innerCore = new THREE.Mesh(coreGeo, coreMat)
  rootGroup.add(innerCore)

  // 7. Core Inner Glow Breathing Sphere
  const glowGeo = new THREE.SphereGeometry(1.2, 32, 32)
  const glowMat = new THREE.MeshBasicMaterial({
    color: 0x22d3ee,
    transparent: true,
    opacity: 0.35,
    blending: THREE.AdditiveBlending
  })
  innerGlowSphere = new THREE.Mesh(glowGeo, glowMat)
  rootGroup.add(innerGlowSphere)

  // 8. Outer Quantum Lattice (Wireframe Polyhedron)
  const latticeGeo = new THREE.IcosahedronGeometry(2.35, 1)
  const latticeMat = new THREE.MeshBasicMaterial({
    color: 0x38bdf8,
    wireframe: true,
    transparent: true,
    opacity: 0.45
  })
  outerLattice = new THREE.Mesh(latticeGeo, latticeMat)
  rootGroup.add(outerLattice)

  // 9. Streaming Particles Swirl (Orbits & Cosmic Halo)
  const particleCount = 180
  const positions = new Float32Array(particleCount * 3)
  const colors = new Float32Array(particleCount * 3)
  const colorPalette = [
    new THREE.Color(0x06b6d4), // Cyan
    new THREE.Color(0x10b981), // Emerald
    new THREE.Color(0x8b5cf6), // Violet
    new THREE.Color(0xf97316), // Orange
    new THREE.Color(0x38bdf8)  // Sky
  ]

  for (let i = 0; i < particleCount; i++) {
    const i3 = i * 3
    let radius = 0
    let theta = 0
    let phi = 0

    // 65% Particles on Equatorial Disk Orbit (Traffic Flow)
    if (i < particleCount * 0.65) {
      radius = 2.6 + Math.random() * 2.8
      theta = Math.random() * Math.PI * 2
      const zOffset = (Math.random() - 0.5) * 0.9
      positions[i3] = Math.cos(theta) * radius
      positions[i3 + 1] = Math.sin(theta) * radius * 0.3 + (Math.random() - 0.5) * 0.4
      positions[i3 + 2] = zOffset * radius
    } else {
      // 35% Particles in Surrounding Spherical Halo
      radius = 3.0 + Math.random() * 3.2
      theta = Math.random() * Math.PI * 2
      phi = Math.acos(Math.random() * 2 - 1)
      positions[i3] = radius * Math.sin(phi) * Math.cos(theta)
      positions[i3 + 1] = radius * Math.sin(phi) * Math.sin(theta)
      positions[i3 + 2] = radius * Math.cos(phi)
    }

    const chosenColor = colorPalette[Math.floor(Math.random() * colorPalette.length)]
    colors[i3] = chosenColor.r
    colors[i3 + 1] = chosenColor.g
    colors[i3 + 2] = chosenColor.b
  }

  const particleGeo = new THREE.BufferGeometry()
  particleGeo.setAttribute('position', new THREE.BufferAttribute(positions, 3))
  particleGeo.setAttribute('color', new THREE.BufferAttribute(colors, 3))

  const particleMat = new THREE.PointsMaterial({
    size: 0.05,
    vertexColors: true,
    transparent: true,
    opacity: 0.85,
    blending: THREE.AdditiveBlending
  })
  particlesMesh = new THREE.Points(particleGeo, particleMat)
  rootGroup.add(particlesMesh)

  // 10. Build 6 3D Holographic Satellites & Connecting Laser Beams
  satellites.length = 0
  hitMeshes.length = 0

  PROVIDERS.forEach((prov) => {
    const targetVector = new THREE.Vector3(...prov.position)

    // Holographic Canvas Badge Texture with Official SVG
    const badgeTexture = createHolographicBadgeTexture(prov)
    const spriteMat = new THREE.SpriteMaterial({
      map: badgeTexture,
      transparent: true,
      opacity: 0.98
    })
    const sprite = new THREE.Sprite(spriteMat)
    sprite.scale.set(0.95, 0.95, 1.0)
    sprite.position.copy(targetVector)
    rootGroup?.add(sprite)

    // Invisible Hit Sphere for precise Raycaster hover detection
    const hitGeo = new THREE.SphereGeometry(0.65, 8, 8)
    const hitMat = new THREE.MeshBasicMaterial({ visible: false })
    const hitMesh = new THREE.Mesh(hitGeo, hitMat)
    hitMesh.position.copy(targetVector)
    hitMesh.userData = { providerId: prov.id }
    rootGroup?.add(hitMesh)
    hitMeshes.push(hitMesh)

    // Glowing Rotating Outer Halo Ring
    const haloGeo = new THREE.RingGeometry(0.55, 0.62, 32)
    const haloMat = new THREE.MeshBasicMaterial({
      color: prov.color,
      side: THREE.DoubleSide,
      transparent: true,
      opacity: 0.55
    })
    const haloMesh = new THREE.Mesh(haloGeo, haloMat)
    haloMesh.position.copy(targetVector)
    rootGroup?.add(haloMesh)

    // Connecting Data Beam (Laser line from center core to satellite)
    const beamPoints = [new THREE.Vector3(0, 0, 0), targetVector]
    const beamGeo = new THREE.BufferGeometry().setFromPoints(beamPoints)
    const beamMat = new THREE.LineBasicMaterial({
      color: prov.color,
      transparent: true,
      opacity: 0.4
    })
    const beamLine = new THREE.Line(beamGeo, beamMat)
    rootGroup?.add(beamLine)

    // Travelling Photons (Data Packets flying along the beam)
    const photonCount = 3
    const photons: THREE.Mesh[] = []
    const photonGeo = new THREE.SphereGeometry(0.08, 12, 12)
    const photonMat = new THREE.MeshBasicMaterial({
      color: 0xffffff,
      transparent: true,
      opacity: 0.9
    })

    for (let p = 0; p < photonCount; p++) {
      const photonMesh = new THREE.Mesh(photonGeo, photonMat)
      photonMesh.position.set(0, 0, 0)
      rootGroup?.add(photonMesh)
      photons.push(photonMesh)
    }

    satellites.push({
      id: prov.id,
      info: prov,
      sprite,
      hitMesh,
      beam: beamLine,
      halo: haloMesh,
      photons,
      originalColor: prov.color,
      targetPos: targetVector
    })
  })

  // Start Animation
  animate()
}

let clock = new THREE.Clock()

function animate() {
  if (!isVisible) {
    animationFrameId = requestAnimationFrame(animate)
    return
  }

  const delta = Math.min(clock.getDelta(), 0.1)
  const elapsedTime = clock.getElapsedTime()

  // 1. Damped Mouse Physics (Smooth Inertia)
  currentRotationX += (targetRotationX - currentRotationX) * 0.05
  currentRotationY += (targetRotationY - currentRotationY) * 0.05

  // Continuous auto-rotation without angle jumps when mouse hovers
  autoRotationY += delta * 0.12
  if (rootGroup) {
    rootGroup.rotation.x = currentRotationX
    rootGroup.rotation.y = autoRotationY + currentRotationY
  }

  // 2. Quantum Core Pulsation
  if (innerCore) {
    innerCore.rotation.x = elapsedTime * 0.2
    innerCore.rotation.y = elapsedTime * 0.25
    const coreScale = 1.0 + Math.sin(elapsedTime * 2.2) * 0.05
    innerCore.scale.set(coreScale, coreScale, coreScale)
  }

  // 3. Inner Glow Breathing
  if (innerGlowSphere) {
    const glowScale = 1.0 + Math.sin(elapsedTime * 2.8) * 0.1
    innerGlowSphere.scale.set(glowScale, glowScale, glowScale)
  }

  // 4. Reverse Rotating Outer Lattice
  if (outerLattice) {
    outerLattice.rotation.x = -elapsedTime * 0.14
    outerLattice.rotation.y = -elapsedTime * 0.18
  }

  // 5. Particles Swirl
  if (particlesMesh) {
    particlesMesh.rotation.y = elapsedTime * 0.06
    particlesMesh.rotation.z = Math.sin(elapsedTime * 0.12) * 0.08
  }

  // 6. Raycast check inside rAF (smooth 60fps, no layout thrashing)
  if (isHovering && camera && hitMeshes.length > 0) {
    raycaster.setFromCamera(mouseVector, camera)
    const intersects = raycaster.intersectObjects(hitMeshes, false)

    if (intersects.length > 0) {
      const hitObj = intersects[0].object
      const provId = hitObj.userData.providerId
      const targetSat = satellites.find((s) => s.id === provId)
      if (targetSat) {
        if (hoveredSatellite.value?.id !== targetSat.info.id) {
          hoveredSatellite.value = targetSat.info
        }

        // Project 3D coordinate to 2D screen space for dynamic tooltip
        targetSat.sprite.getWorldPosition(tempWorldPos)
        tempWorldPos.project(camera)

        const width = containerRect?.width || containerRef.value?.clientWidth || 500
        const height = containerRect?.height || containerRef.value?.clientHeight || 450
        const screenX = ((tempWorldPos.x + 1) * width) / 2
        const screenY = ((-tempWorldPos.y + 1) * height) / 2
        tooltipPos.value = { x: screenX, y: screenY }
      }
    } else {
      if (hoveredSatellite.value !== null) {
        hoveredSatellite.value = null
      }
    }
  }

  // 7. Update Satellites, Halo & Traveling Photons
  satellites.forEach((sat, index) => {
    const isHovered = hoveredSatellite.value?.id === sat.id
    const isSelected = selectedProviderId.value !== 'all' && selectedProviderId.value === sat.id
    const isHighlighted = isHovered || isSelected

    // Scale sprite smoothly without allocating Vector3
    const targetScale = isHighlighted ? 1.25 : 0.95
    sat.sprite.scale.x += (targetScale - sat.sprite.scale.x) * 0.1
    sat.sprite.scale.y += (targetScale - sat.sprite.scale.y) * 0.1

    // Halo breathing & billboard camera facing
    if (camera) {
      sat.halo.lookAt(camera.position)
    }
    const haloScale = (isHighlighted ? 1.25 : 1.0) + Math.sin(elapsedTime * 2.8 + index) * 0.1
    sat.halo.scale.set(haloScale, haloScale, haloScale)

    const haloMat = sat.halo.material as THREE.MeshBasicMaterial
    haloMat.opacity = isHighlighted ? 0.85 : 0.5

    const beamMat = sat.beam.material as THREE.LineBasicMaterial
    beamMat.opacity = isHighlighted ? 0.9 : 0.35

    // Animate travelling photons from zeroVector to targetPos
    sat.photons.forEach((photon, pIdx) => {
      const speed = isHighlighted ? 0.95 : 0.55
      const offset = pIdx * 0.33
      const progress = (elapsedTime * speed + offset) % 1.0

      photon.position.lerpVectors(zeroVector, sat.targetPos, progress)
      // Fade out at ends
      const alpha = Math.sin(progress * Math.PI)
      const mat = photon.material as THREE.MeshBasicMaterial
      mat.opacity = alpha * (isHighlighted ? 1.0 : 0.85)
    })
  })

  // 8. Render
  if (renderer && scene && camera) {
    renderer.render(scene, camera)
  }

  animationFrameId = requestAnimationFrame(animate)
}

// Watch active provider prop
watch(
  () => props.activeProvider,
  (val) => {
    if (val) {
      selectedProviderId.value = val
    }
  }
)

function onWindowResize() {
  if (!containerRef.value || !renderer || !camera) return
  containerRect = containerRef.value.getBoundingClientRect()
  const width = containerRect.width
  const height = containerRect.height || 450
  camera.aspect = width / height
  camera.updateProjectionMatrix()
  renderer.setSize(width, height)
}

onMounted(() => {
  initThree()

  // Setup ResizeObserver
  if (containerRef.value) {
    resizeObserver = new ResizeObserver(() => {
      onWindowResize()
    })
    resizeObserver.observe(containerRef.value)
  }

  // Setup IntersectionObserver for 60 FPS battery efficiency
  if (containerRef.value) {
    intersectionObserver = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          isVisible = entry.isIntersecting
        })
      },
      { threshold: 0.1 }
    )
    intersectionObserver.observe(containerRef.value)
  }
})

onBeforeUnmount(() => {
  if (animationFrameId !== null) {
    cancelAnimationFrame(animationFrameId)
  }
  if (resizeObserver) resizeObserver.disconnect()
  if (intersectionObserver) intersectionObserver.disconnect()

  // Clean Three.js resources
  if (renderer) {
    renderer.dispose()
    if (renderer.domElement && renderer.domElement.parentNode) {
      renderer.domElement.parentNode.removeChild(renderer.domElement)
    }
  }
})
</script>

<template>
  <div
    ref="containerRef"
    class="group relative flex h-[400px] w-full cursor-grab items-center justify-center overflow-hidden sm:h-[480px] lg:h-[540px] active:cursor-grabbing"
    @mousemove="handleMouseMove"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
  >
    <!-- Background Radial Ambient Glows -->
    <div class="pointer-events-none absolute inset-0 flex items-center justify-center">
      <div class="h-64 w-64 rounded-full bg-cyan-500/15 blur-3xl transition-transform duration-700 group-hover:scale-125 dark:bg-cyan-500/20"></div>
      <div class="absolute h-80 w-80 rounded-full bg-violet-600/10 blur-[100px] transition-transform duration-700 group-hover:scale-110 dark:bg-violet-600/15"></div>
      <div class="absolute h-56 w-56 rounded-full bg-emerald-500/10 blur-3xl dark:bg-emerald-500/15"></div>
    </div>

    <!-- Floating Mission Control Tooltip (Appears when hovering on 3D satellite) -->
    <transition
      enter-active-class="transition duration-200 ease-out"
      enter-from-class="opacity-0 translate-y-2 scale-95"
      enter-to-class="opacity-100 translate-y-0 scale-100"
      leave-active-class="transition duration-150 ease-in"
      leave-from-class="opacity-100 translate-y-0 scale-100"
      leave-to-class="opacity-0 translate-y-1 scale-95"
    >
      <div
        v-if="hoveredSatellite"
        class="pointer-events-none absolute z-20 -translate-x-1/2 -translate-y-full rounded-2xl border border-gray-200/90 bg-white/95 p-3.5 shadow-2xl shadow-cyan-500/20 backdrop-blur-xl dark:border-white/20 dark:bg-dark-950/95 sm:p-4"
        :style="{
          left: `${Math.max(120, Math.min(tooltipPos.x, (containerRef?.clientWidth || 500) - 120))}px`,
          top: `${Math.max(60, tooltipPos.y - 18)}px`
        }"
      >
        <div class="flex items-center gap-3">
          <div class="flex h-9 w-9 items-center justify-center rounded-xl border border-gray-100 bg-gray-50 p-1.5 shadow-inner dark:border-white/10 dark:bg-dark-900">
            <img
              :src="hoveredSatellite.logoUrl"
              :alt="hoveredSatellite.name"
              class="h-6 w-6 object-contain"
              :class="{ 'dark:invert': hoveredSatellite.isMonochrome }"
            />
          </div>
          <div>
            <div class="flex items-center gap-2">
              <span class="font-bold text-gray-900 dark:text-white">{{ hoveredSatellite.name }}</span>
              <span
                class="rounded-full border px-2 py-0.5 font-mono text-[10px] font-semibold"
                :style="{
                  borderColor: `${hoveredSatellite.colorHex}40`,
                  backgroundColor: `${hoveredSatellite.colorHex}15`,
                  color: hoveredSatellite.colorHex
                }"
              >
                {{ hoveredSatellite.badge }}
              </span>
            </div>
            <p class="font-mono text-xs text-gray-500 dark:text-dark-400">
              {{ hoveredSatellite.subtitle }}
            </p>
          </div>
        </div>

        <div class="mt-2.5 flex items-center justify-between border-t border-gray-100 pt-2 font-mono text-[11px] dark:border-white/5">
          <span class="text-gray-600 dark:text-gray-300">{{ hoveredSatellite.feature }}</span>
          <span class="font-bold text-emerald-600 dark:text-emerald-400">&lt; {{ hoveredSatellite.latency }}</span>
        </div>
      </div>
    </transition>

    <!-- Top Floating Mission Control Selector Bar (Clickable Models) -->
    <div class="absolute top-3 z-10 hidden max-w-full flex-wrap items-center justify-center gap-1.5 px-3 sm:flex">
      <button
        v-for="prov in PROVIDERS"
        :key="prov.id"
        @click.stop="selectProvider(prov.id)"
        class="flex items-center gap-1.5 rounded-full border px-2.5 py-1 font-mono text-[11px] font-semibold transition-all duration-300 hover:scale-105"
        :class="[
          selectedProviderId === prov.id
            ? 'scale-105 border-cyan-400 bg-cyan-500/20 text-cyan-600 shadow-md shadow-cyan-500/20 dark:text-cyan-300'
            : 'border-gray-200/80 bg-white/70 text-gray-600 hover:border-gray-300 dark:border-white/10 dark:bg-dark-950/60 dark:text-dark-300 dark:hover:border-white/20'
        ]"
      >
        <img
          :src="prov.logoUrl"
          :alt="prov.name"
          class="h-3.5 w-3.5 object-contain"
          :class="{ 'dark:invert': prov.isMonochrome }"
        />
        <span>{{ prov.name.split(' ')[0] }}</span>
        <span
          class="h-1.5 w-1.5 rounded-full"
          :style="{ backgroundColor: prov.colorHex }"
        ></span>
      </button>

      <button
        v-if="selectedProviderId !== 'all'"
        @click.stop="selectedProviderId = 'all'"
        class="rounded-full border border-gray-300 bg-white/80 px-2 py-1 font-mono text-[10px] text-gray-500 hover:text-gray-900 dark:border-white/10 dark:bg-dark-900/80 dark:text-dark-300 dark:hover:text-white"
        title="Hiển thị tất cả vệ tinh"
      >
        ✕ Đặt lại
      </button>
    </div>

    <!-- Bottom Center Status Pill -->
    <div
      class="pointer-events-none absolute bottom-2 z-10 flex items-center gap-2 rounded-full border border-cyan-500/30 bg-white/80 px-4 py-1 shadow-lg shadow-cyan-500/10 backdrop-blur-lg dark:bg-dark-950/80"
    >
      <span class="relative flex h-2 w-2">
        <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-cyan-400 opacity-75"></span>
        <span class="relative inline-flex h-2 w-2 rounded-full bg-cyan-500"></span>
      </span>
      <span class="font-mono text-[11px] font-medium tracking-wide text-cyan-700 dark:text-cyan-300">
        SUB2API QUANTUM ROUTING CORE • 60 FPS
      </span>
    </div>
  </div>
</template>
