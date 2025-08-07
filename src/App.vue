<script>
import AboutWindow from './components/AboutWindow.vue'
import ProjectsWindow from './components/ProjectsWindow.vue'
import SkillsWindow from './components/ExperiencesWindow.vue'
import ContactWindow from './components/ContactWindow.vue'
import TypingText from './components/TypingText.vue'
import { Motion } from '@motionone/vue'

export default {
  components: {
    AboutWindow,
    ProjectsWindow,
    SkillsWindow,
    ContactWindow,
    TypingText,
    Motion
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
      dockHovered: false,
    }
  },
  computed: {
    windowComponent() {
      switch (this.openWindow) {
        case 'fa-regular fa-user': return 'AboutWindow'
        case 'fa-folder-open': return 'ProjectsWindow'
        case 'fa-briefcase': return 'SkillsWindow'
        case 'fa-envelope': return 'ContactWindow'
        default: return null
      }
    }
  },
  methods: {
    updateTime() {
      const now = new Date();
      this.currentTime = now.toLocaleTimeString().toUpperCase();

      const daysOfWeek = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
      const dayName = daysOfWeek[now.getDay()];

      const year = now.getFullYear();
      const month = String(now.getMonth() + 1).padStart(2, '0'); // Months are 0-indexed
      const day = String(now.getDate()).padStart(2, '0');

      const formattedDate = `${year}-${month}-${day} (${dayName})`;
      this.currentDate = formattedDate;
    },
    openIconWindow(icon) {
      if (!this.openWindows.includes(icon)) {
        // Calculate offset for new window
        const offsetStep = 32;
        // Place the first window higher by using a negative baseOffset
        const baseOffset = -96;
        const openCount = this.openWindows.length;
        this.openWindows.push(icon);
        this.$nextTick(() => {
          const win = document.getElementById(`window-${icon}`);
          if (win) {
            const width = window.innerWidth * 0.5;
            const height = window.innerHeight * 0.5;
            // Offset each new window by offsetStep px from the previous
            const x = (window.innerWidth - width) / 2 + baseOffset + openCount * offsetStep;
            const y = Math.max((window.innerHeight - height) / 3, 48) + baseOffset + openCount * offsetStep;

            let initialY = y; // Default desktop y position

            // Add this condition to set the initial Y for mobile
            if (this.windowWidth < 768) {
              initialY = window.innerHeight; // Start off-screen at the bottom
            }

            this.windowStates[icon] = {
              x: x,
              y: initialY,
              dragging: false,
              dragOffset: { x: 0, y: 0 },
              zIndex: this.topZIndex++,
              maximized: this.windowWidth < 768 // Automatically maximize on mobile
            };
          }
        });
      } else {
        this.bringToFront(icon);
      }
    },
    bringToFront(icon) {
      if (this.windowStates[icon]) {
        this.windowStates[icon].zIndex = this.topZIndex++;
      }
    },
    closeWindow(icon) {
      this.openWindows = this.openWindows.filter(win => win !== icon);
      delete this.windowStates[icon];

      // Bring the new front-most window to front
      if (this.openWindows.length > 0) {
        const frontIcon = this.openWindows[this.openWindows.length - 1];
        if (this.windowStates[frontIcon]) {
          this.windowStates[frontIcon].zIndex = this.topZIndex++;
        }
      } else {
        this.dockHovered = false;
      }
    },
    startDrag(e, icon) {
      // Only allow dragging on desktop
      if (this.windowWidth < 768) return;

      const state = this.windowStates[icon];
      if (!state) return;

      this.bringToFront(icon);

      state.dragging = true;
      const clientX = e.type.startsWith('touch') ? e.touches[0].clientX : e.clientX;
      const clientY = e.type.startsWith('touch') ? e.touches[0].clientY : e.clientY;
      state.dragOffset = {
        x: clientX - state.x,
        y: clientY - state.y
      };

      const moveHandler = ev => this.onDrag(ev, icon);
      const stopHandler = () => this.stopDrag(icon, moveHandler, stopHandler);

      window.addEventListener('mousemove', moveHandler);
      window.addEventListener('mouseup', stopHandler);
      window.addEventListener('touchmove', moveHandler, { passive: false });
      window.addEventListener('touchend', stopHandler);
    },
    onDrag(e, icon) {
      const state = this.windowStates[icon];
      if (!state || !state.dragging) return;
      if (e.type.startsWith('touch')) e.preventDefault();

      const clientX = e.type.startsWith('touch') ? e.touches[0].clientX : e.clientX;
      const clientY = e.type.startsWith('touch') ? e.touches[0].clientY : e.clientY;
      state.x = clientX - state.dragOffset.x;
      state.y = Math.max(clientY - state.dragOffset.y, 48);
    },
    stopDrag(icon, moveHandler, stopHandler) {
      const state = this.windowStates[icon];
      if (state) state.dragging = false;

      window.removeEventListener('mousemove', moveHandler);
      window.removeEventListener('mouseup', stopHandler);
      window.removeEventListener('touchmove', moveHandler);
      window.removeEventListener('touchend', stopHandler);
    },
    getWindowStyle(icon) {
      const state = this.windowStates[icon] || {};
      const topBarHeight = 48;
      const dockHeight = 148;
      const availableHeight = `calc(100vh - ${topBarHeight}px - ${dockHeight}px)`;

      // Always maximize on mobile
      if (this.windowWidth < 768) {
        this.windowStates[icon] = {
          ...this.windowStates[icon],
        };
        return {
          left: 0,
          top: `${topBarHeight}px`,
          width: '100vw',
          height: `calc(100vh - ${topBarHeight}px)`,
          zIndex: state.zIndex || 1
        };
      }
      else {
        const x = state.x || 0;
        const y = Math.max(state.y || topBarHeight, topBarHeight);
        return {
          left: `${x}px`,
          top: `${y}px`,
          width: '50vw',
          maxHeight: '50vh',
          height: 'min-content',
          zIndex: state.zIndex || 1
        };
      }
    },
    title(icon) {
      switch (icon) {
        case 'fa-user': return this.windowWidth < 768 ? 'About' : 'About_Me';
        case 'fa-briefcase': return this.windowWidth < 768 ? 'Experience' : 'My_Experience'
        case 'fa-folder-open': return this.windowWidth < 768 ? 'Projects' : 'My_Projects'
        case 'fa-envelope': return this.windowWidth < 768 ? 'Contact' : 'Contact_Me'
        default: return null
      }
    },
    // handleResize() {
    //   this.windowWidth = window.innerWidth;
    //   // Update maximized state for all windows when screen size changes
    //   this.openWindows.forEach(icon => {
    //     if (this.windowStates[icon]) {
    //       this.windowStates[icon].maximized = this.windowWidth < 768;
    //     }
    //   });
    // }
  },
  mounted() {
    this.updateTime();
    this.interval = setInterval(this.updateTime, 1000);
    this.startTypingAnimation();
    // Add window resize listener
    window.addEventListener('resize', this.handleResize);
  },
  beforeDestroy() {
    clearInterval(this.interval);
    this.stopDrag();
    // Remove window resize listener
    window.removeEventListener('resize', this.handleResize);
  }
}
</script>

<template>
  <div class="flex flex-col h-screen justify-center items-center prevent-select">
    <!-- Top bar layout -->
    <div class="p-4 border border-gray-200 shadow-2xl bg-white w-full h-12 flex justify-between items-center">
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
          class="transform transition-all duration-300 p-6 grid grid-cols-4 rounded-3xl shadow-2xl items-center text-4xl gap-x-6 sm:w-full m-2 md:w-fit bg-white border border-gray-200">
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
    <div v-for="icon in openWindows" :key="icon" class="fixed z-50 w-[100vw] md:max-w-3xl" :id="`window-${icon}`"
      :style="getWindowStyle(icon)" @click="bringToFront(icon)">
      <div
        class="flex md:max-h-[85vh] flex-col bg-white border border-gray-200 rounded-t-2xl md:rounded-3xl shadow-2xl w-full h-full">
        <!-- Window Header -->
        <div class="flex items-center justify-between py-2 px-4 rounded-t-3xl md:bg-gray-50 shadow-sm select-none"
          @mousedown="e => startDrag(e, icon)" @touchstart="e => startDrag(e, icon)">
          <!-- Icon -->
          <i :class="['fas', icon, 'ml-1 text-lg font-bold text-gray-700 invisible md:visible']"></i>
          <!-- Title -->
          <span class="font-bold text-lg tracking-wide truncate text-gray-700">
            {{ title(icon) }}
          </span>
          <!-- Close Button -->
          <button
            class="ml-2 md:bg-gray-200 hover:bg-red-400 hover:text-white cursor-pointer rounded-full w-8 h-8 flex items-center justify-center md:shadow focus:outline-none pointer-events-auto"
            @click.stop="() => closeWindow(icon)">
            <span :class="['font-bold', windowWidth < 768 ? 'fa fa-chevron-down' : 'fa fa-xmark']"></span>
          </button>
        </div>
        <div class="flex flex-col items-center flex-grow overflow-auto bg-white rounded-b-3xl 
            [&::-webkit-scrollbar]:w-1
            [&::-webkit-scrollbar-thumb]:rounded-full
            [&::-webkit-scrollbar-thumb]:bg-gray-300">
          <!-- Window Content -->
          <component :is="{
            'fa-user': 'AboutWindow',
            'fa-briefcase': 'SkillsWindow',
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
</style>
