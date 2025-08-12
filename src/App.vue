<script>
import AboutWindow from './components/AboutWindow.vue'
import ProjectsWindow from './components/ProjectsWindow.vue'
import ExperiencesWindow from './components/ExperiencesWindow.vue'
import ContactWindow from './components/ContactWindow.vue'
import TypingText from './components/TypingText.vue'

// Simple debounce helper
function debounce(fn, delay) {
  let timeout
  return (...args) => {
    clearTimeout(timeout)
    timeout = setTimeout(() => fn(...args), delay)
  }
}

// Simple throttle helper
function throttle(fn, limit) {
  let inThrottle
  return (...args) => {
    if (!inThrottle) {
      fn(...args)
      inThrottle = true
      setTimeout(() => (inThrottle = false), limit)
    }
  }
}

export default {
  components: {
    AboutWindow,
    ProjectsWindow,
    ExperiencesWindow,
    ContactWindow,
    TypingText
  },
  data() {
    return {
      icons: ['fa-user', 'fa-briefcase', 'fa-folder-open', 'fa-envelope'],
      currentTime: '',
      currentDate: '',
      openWindows: [],
      windowStates: {},
      topZIndex: 1,
      windowWidth: window.innerWidth,
      dockHovered: false
    }
  },
  computed: {
    isMobile() {
      return this.windowWidth < 768
    }
  },
  methods: {
    updateTime() {
      const now = new Date()
      this.currentTime = now.toLocaleTimeString().toUpperCase()
      const daysOfWeek = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"]
      const dayName = daysOfWeek[now.getDay()]
      const year = now.getFullYear()
      const month = String(now.getMonth() + 1).padStart(2, '0')
      const day = String(now.getDate()).padStart(2, '0')
      this.currentDate = `${year}-${month}-${day} (${dayName})`
    },
    openIconWindow(icon) {
      if (!this.openWindows.includes(icon)) {
        const offsetStep = 32
        const baseOffset = -96
        const openCount = this.openWindows.length
        this.openWindows.push(icon)

        this.$nextTick(() => {
          const width = window.innerWidth * 0.5
          const height = window.innerHeight * 0.5
          const x = (window.innerWidth - width) / 2 + baseOffset + openCount * offsetStep
          const yDesktop = Math.max((window.innerHeight - height) / 3, 48) + baseOffset + openCount * offsetStep

          this.windowStates[icon] = {
            x: x,
            y: this.isMobile ? window.innerHeight : yDesktop,
            dragging: false,
            dragOffset: { x: 0, y: 0 },
            zIndex: this.topZIndex++,
            maximized: this.isMobile
          }

          if (this.isMobile) {
            const el = document.getElementById(`window-${icon}`)
            if (el) {
              el.classList.add('slide-up')
              setTimeout(() => el.classList.remove('slide-up'), 400) // remove after animation
            }
          }
        })
      } else {
        this.bringToFront(icon)
      }
    },
    bringToFront(icon) {
      if (this.windowStates[icon]) {
        this.windowStates[icon].zIndex = this.topZIndex++
      }
    },
    closeWindow(icon) {
      if (this.isMobile) {
        const el = document.getElementById(`window-${icon}`)
        if (el) {
          el.classList.add('slide-down')
          setTimeout(() => {
            this.openWindows = this.openWindows.filter(win => win !== icon)
            delete this.windowStates[icon]
            if (this.openWindows.length > 0) {
              this.bringToFront(this.openWindows[this.openWindows.length - 1])
            } else {
              this.dockHovered = false
            }
          }, 200)
          return
        }
      }
      this.openWindows = this.openWindows.filter(win => win !== icon)
      delete this.windowStates[icon]
      if (this.openWindows.length > 0) {
        this.bringToFront(this.openWindows[this.openWindows.length - 1])
      } else {
        this.dockHovered = false
      }
    },
    startDrag(e, icon) {
      if (this.isMobile) return
      const state = this.windowStates[icon]
      if (!state) return
      this.bringToFront(icon)
      state.dragging = true

      const clientX = e.type.startsWith('touch') ? e.touches[0].clientX : e.clientX
      const clientY = e.type.startsWith('touch') ? e.touches[0].clientY : e.clientY
      state.dragOffset = { x: clientX - state.x, y: clientY - state.y }

      const moveHandler = throttle(ev => this.onDrag(ev, icon), 16)
      const stopHandler = () => this.stopDrag(icon, moveHandler, stopHandler)

      window.addEventListener('mousemove', moveHandler)
      window.addEventListener('mouseup', stopHandler)
      window.addEventListener('touchmove', moveHandler, { passive: false })
      window.addEventListener('touchend', stopHandler)
    },
    onDrag(e, icon) {
      const state = this.windowStates[icon]
      if (!state || !state.dragging) return
      if (e.type.startsWith('touch')) e.preventDefault()
      const clientX = e.type.startsWith('touch') ? e.touches[0].clientX : e.clientX
      const clientY = e.type.startsWith('touch') ? e.touches[0].clientY : e.clientY
      state.x = Math.max(0, clientX - state.dragOffset.x)
      state.y = Math.max(48, clientY - state.dragOffset.y)
    },
    stopDrag(icon, moveHandler, stopHandler) {
      const state = this.windowStates[icon]
      if (state) state.dragging = false
      window.removeEventListener('mousemove', moveHandler)
      window.removeEventListener('mouseup', stopHandler)
      window.removeEventListener('touchmove', moveHandler)
      window.removeEventListener('touchend', stopHandler)
    },
    getWindowStyle(icon) {
      const state = this.windowStates[icon] || {}
      if (this.isMobile) {
        return {
          left: 0,
          top: 0,
          width: '100vw',
          height: '100dvh',
          zIndex: state.zIndex || 1
        }
      }
      const topBarHeight = 48
      return {
        left: `${Math.min(state.x || 0, window.innerWidth - 100)}px`,
        top: `${Math.max(state.y || topBarHeight, topBarHeight)}px`,
        width: '50vw',
        maxHeight: '50dvh',
        height: 'min-content',
        zIndex: state.zIndex || 1
      }
    },
    title(icon) {
      const titles = {
        'fa-user': this.isMobile ? 'About' : 'About_Me',
        'fa-briefcase': this.isMobile ? 'Experience' : 'My_Experience',
        'fa-folder-open': this.isMobile ? 'Projects' : 'My_Projects',
        'fa-envelope': this.isMobile ? 'Contact' : 'Contact_Me'
      }
      return titles[icon] || ''
    },
    handleResize() {
      this.windowWidth = window.innerWidth
      // Adjust positions if switching from mobile <-> desktop
      this.openWindows.forEach(icon => {
        if (this.isMobile) {
          this.windowStates[icon].maximized = true
        } else {
          this.windowStates[icon].maximized = false
        }
      })
    }
  },
  mounted() {
    this.updateTime()
    this.interval = setInterval(this.updateTime, 1000)

    // Wrap in debounce here so `this` is bound correctly
    const debouncedResize = debounce(this.handleResize.bind(this), 150)
    window.addEventListener('resize', debouncedResize)

    // Keep reference so we can remove it later
    this._debouncedResize = debouncedResize
  },
  beforeUnmount() {
    clearInterval(this.interval)
    window.removeEventListener('resize', this._debouncedResize)
  }
}
</script>


<template>
  <div class="flex flex-col h-dvh justify-center items-center prevent-select">
    <!-- Top bar layout -->
    <div class="p-4 h-6 md:h-12 text-xs md:text-base font-bold md:font-normal border border-gray-200 shadow-2xl bg-white w-full flex justify-between items-center">
      <!-- <span class="font-bold text-lg">Petaling Jaya, Malaysia</span> -->
      <span class="font-bold text-gray-700">Ikmal_OS</span>
      <span class="font-bold text-gray-700"><span class="hidden md:inline">{{ currentDate }}</span> {{
        currentTime }}</span>
    </div>
    <!-- "Desktop" layout -->
    <div class="flex flex-grow w-full items-center justify-center bg-gray-50">
      <!-- <div class="w-full max-w-5xl mx-auto p-8 rounded-2xl shadow-lg bg-white flex items-center justify-center"> -->
      <div class="w-full p-6 mx-auto flex items-center justify-center">
        <TypingText />
      </div>
    </div>

    <!-- Dock -->
    <div v-if="windowWidth >= 768 || openWindows.length == 0"
      class="fixed bottom-0 left-1/2 -translate-x-1/2 w-full flex justify-center transition-transform hover:scale-105"
      :class="dockHovered ? 'z-100' : 'z-0'" @mouseenter="dockHovered = true" @mouseleave="dockHovered = false"
      @focusin="dockHovered = true" @focusout="dockHovered = false">
      <div class="relative h-6 flex items-end">
        <div
          class="transform transition-all duration-300 p-4 md:p-6 grid grid-cols-4 rounded-3xl shadow-2xl items-center text-4xl gap-x-6 sm:w-full m-2 md:w-fit bg-white border border-gray-200">
          <div v-for="icon in icons" :key="icon" class="relative group flex flex-col items-center justify-center">
            <!-- Icon label -->
            <span
              class="absolute left-1/2 -translate-x-1/2 bg-gray-900 text-white text-xs text-nowrap px-2 py-1 rounded shadow-lg -top-8 opacity-0 group-hover:opacity-100 transition-opacity">
              {{ title(icon) }}
            </span>
            <!-- Button -->
            <button
              class="transition-transform cursor-pointer hover:scale-100 flex rounded-full w-16 h-16 justify-center items-center bg-white text-gray-700 shadow hover:bg-blue-50 hover:text-blue-600 border border-gray-200 transition-all duration-150 focus:outline-none"
              :class="{
                'ring-2 ring-blue-400': windowStates[icon] && windowStates[icon].zIndex === topZIndex - 1 && openWindows.includes(icon)
              }" @click="openIconWindow(icon)">
              <i :class="['fas', icon]"></i>
            </button>
            <span v-if="windowWidth < 768" class="text-xs text-gray-700 font-bold mt-2">{{ title(icon) }}</span>
            <!-- Active window indicator -->
            <span v-if="windowStates[icon] && openWindows.includes(icon)"
              class="absolute left-1/2 -translate-x-1/2 top-full mt-1 h-1 rounded"
              :class="windowStates[icon].zIndex === topZIndex - 1 ? 'bg-blue-500 w-full opacity-100' : 'bg-gray-300 w-1/2'"></span>
          </div>
        </div>
      </div>
    </div>

    <!-- Popup Windows -->
    <div v-for="icon in openWindows" :key="icon" :class="[
      'fixed z-50',
      isMobile ? 'w-screen h-dvh top-0 left-0 rounded-none' : 'w-[100vw] md:max-w-3xl',
    ]" :id="`window-${icon}`" :style="getWindowStyle(icon)" @click="bringToFront(icon)">
      <div :class="[
        'flex flex-col bg-white border border-gray-200 shadow-2xl w-full h-full',
        isMobile ? 'rounded-none max-h-none' : 'md:max-h-[85vh] rounded-t-2xl md:rounded-3xl'
      ]">
        <!-- Window Header -->
        <div class="flex items-center justify-between py-2 px-4 rounded-t-3xl md:bg-gray-50 shadow-sm select-none"
          @mousedown="e => startDrag(e, icon)" @touchstart="e => startDrag(e, icon)">
          <!-- Icon -->
          <i :class="['fas', icon, 'ml-1 text-lg font-bold text-gray-700 invisible md:visible']"></i>
          <!-- Title -->
          <span class="font-bold md:text-lg tracking-wide truncate text-gray-700">
            {{ title(icon) }}
          </span>
          <!-- Close Button -->
          <button
            class="ml-2 md:bg-gray-200 hover:bg-red-400 hover:text-white cursor-pointer rounded-full w-8 h-8 flex items-center justify-center md:shadow focus:outline-none pointer-events-auto"
            @click.stop="() => closeWindow(icon)">
            <span :class="['font-bold', windowWidth < 768 ? 'fa fa-chevron-down' : 'fa fa-xmark']"></span>
          </button>
        </div>
        <div class="flex flex-col items-center flex-grow overflow-y-auto bg-white rounded-b-3xl min-h-0
            [&::-webkit-scrollbar]:w-1
            [&::-webkit-scrollbar-thumb]:rounded-full
            [&::-webkit-scrollbar-thumb]:bg-gray-300">
          <!-- Window Content -->
          <component :is="{
            'fa-user': 'AboutWindow',
            'fa-briefcase': 'ExperiencesWindow',
            'fa-folder-open': 'ProjectsWindow',
            'fa-envelope': 'ContactWindow'
          }[icon]" />
        </div>
      </div>
    </div>
  </div>
</template>

<style>
/* Blinking visible/invisible typing cursor */
.typing-cursor-blink {
  animation: blink-opacity 1s steps(1) infinite;
}

@keyframes blink-opacity {

  0%,
  100% {
    opacity: 1;
  }

  50% {
    opacity: 0;
  }
}

@keyframes slideUp {
  from {
    transform: translateY(100%);
    opacity: 1;
  }

  to {
    transform: translateY(0);
    opacity: 1;
  }
}

@keyframes slideDown {
  from {
    transform: translateY(0);
    opacity: 1;
  }

  to {
    transform: translateY(100%);
    opacity: 1;
  }
}

.slide-up {
  animation: slideUp 0.2s ease-out forwards;
}

.slide-down {
  animation: slideDown 0.2s ease-in forwards;
}
</style>
