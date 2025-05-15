<script>
import AboutWindow from './components/AboutWindow.vue'
import ProjectsWindow from './components/ProjectsWindow.vue'
import SkillsWindow from './components/SkillsWindow.vue'
import ContactWindow from './components/ContactWindow.vue'

export default {
  components: {
    AboutWindow,
    ProjectsWindow,
    SkillsWindow,
    ContactWindow
  },
  data() {
    return {
      icons: ['fa-user', 'fa-briefcase', 'fa-code', 'fa-envelope'],
      currentTime: '',
      openWindows: [],
      windowStates: {},
      topZIndex: 1
    }
  },
  computed: {
    windowComponent() {
      switch (this.openWindow) {
        case 'fa-user': return 'AboutWindow'
        case 'fa-briefcase': return 'ProjectsWindow'
        case 'fa-code': return 'SkillsWindow'
        case 'fa-envelope': return 'ContactWindow'
        default: return null
      }
    }
  },
  methods: {
    updateTime() {
      const now = new Date();
      this.currentTime = now.toLocaleTimeString().toUpperCase();
    },
    openIconWindow(icon) {
      if (!this.openWindows.includes(icon)) {
        this.openWindows.push(icon);
        this.$nextTick(() => {
          const win = document.getElementById(`window-${icon}`);
          if (win) {
            const width = window.innerWidth * 0.5;  // 50% of viewport width
            const height = window.innerHeight * 0.5; // 50% of viewport height
            const x = (window.innerWidth - width) / 2;
            const y = (window.innerHeight - height) / 2;
            this.windowStates[icon] = {
              x: x,
              y: y,
              dragging: false,
              dragOffset: { x: 0, y: 0 },
              zIndex: this.topZIndex++
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
    },
    startDrag(e, icon) {
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
      state.y = clientY - state.dragOffset.y;
    },
    stopDrag(icon, moveHandler, stopHandler) {
      const state = this.windowStates[icon];
      if (state) state.dragging = false;

      window.removeEventListener('mousemove', moveHandler);
      window.removeEventListener('mouseup', stopHandler);
      window.removeEventListener('touchmove', moveHandler);
      window.removeEventListener('touchend', stopHandler);
    }
  },
  mounted() {
    this.updateTime();
    this.interval = setInterval(this.updateTime, 1000);
  },
  beforeDestroy() {
    clearInterval(this.interval);
    this.stopDrag();
  }
}
</script>

<template>
  <div class="flex flex-col h-screen justify-center items-center">
    <!-- Top bar layout -->
    <div class="p-2 border w-full flex justify-between items-center">
      <!-- <span class="font-bold text-lg">Petaling Jaya, Malaysia</span> -->
      <span class="font-bold text-lg">Portfolio OS</span>
      <span class="font-bold text-lg">{{ currentTime }}</span>
    </div>
    <!-- "Desktop" layout -->
    <div class="space-x-4 items-center flex flex-grow">
      <span class="text-5xl font-bold cursor-default">Hi, I'm Ikmal.</span>
    </div>
    <!-- Dock layout -->
    <div class="p-8 flex border rounded-xl items-center text-4xl gap-x-6 sm:w-full m-2 md:w-fit">
      <div v-for="icon in icons" :key="icon">
        <button class="flex border rounded-lg w-16 h-16 justify-center items-center
                 bg-white text-black cursor-pointer hover:bg-black hover:text-white" @click="openIconWindow(icon)">
          <i :class="['fas', icon]"></i>
        </button>
      </div>
    </div>

    <!-- Popup Windows -->
    <div v-for="icon in openWindows" :key="icon" class="fixed z-50 md:w-[50vw] md:h-[50vh] w-[100vw] h-[100vh]"
      :id="`window-${icon}`" :style="{
        left: windowStates[icon]?.x + 'px',
        top: windowStates[icon]?.y + 'px',
        zIndex: windowStates[icon]?.zIndex || 1
      }">
      <div class="flex flex-col bg-white rounded-lg border shadow-lg w-full h-full">
        <div class="flex p-2 rounded-t-lg justify-start w-full bg-gray-100 gap-x-2 select-none"
          @mousedown="e => startDrag(e, icon)" @touchstart="e => startDrag(e, icon)">
          <button class="h-4 w-4 rounded-full border bg-red-200 hover:bg-red-300 cursor-pointer"
            @click.stop="() => closeWindow(icon)"></button>
          <button class="h-4 w-4 rounded-full border bg-yellow-200 hover:bg-yellow-300 cursor-pointer"
            @click.stop="() => closeWindow(icon)"></button>
          <button class="h-4 w-4 rounded-full border bg-green-200 hover:bg-green-300 cursor-pointer"
            @click.stop="() => closeWindow(icon)"></button>
        </div>
        <div class="flex flex-col items-center flex-grow justify-center">
          <component :is="{
            'fa-user': 'AboutWindow',
            'fa-briefcase': 'ProjectsWindow',
            'fa-code': 'SkillsWindow',
            'fa-envelope': 'ContactWindow'
          }[icon]" />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
