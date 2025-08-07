<template>
  <span class="text-3xl md:text-5xl font-bold font-mono text-gray-700 text-shadow-md/10">
    {{ displayText }}
    <span class="text-shadow-md/10" :class="highlightClass">{{ displayHighlight }}</span>
    <span class="typing-cursor-blink">|</span>
  </span>
</template>

<script>
export default {
  data() {
    return {
      texts: [
        { text: "Hi! I'm ", highlight: "Ikmal" },
        { text: "I'm a ", highlight: "Control Systems Engineer" },
        { text: "I'm a ", highlight: "Developer" }
      ],
      index: 0,
      charIndex: 0,
      displayText: '',
      displayHighlight: '',
      typingForward: true,
      typingDelay: 100,
      pauseDelay: 1200,
      highlightClass: 'text-blue-500',
    };
  },
  methods: {
    startTyping() {
      const cycle = () => {
        const { text, highlight } = this.texts[this.index];
        const fullLength = text.length + highlight.length;

        if (this.typingForward) {
          if (this.charIndex < fullLength) {
            this.charIndex++;
          } else {
            this.typingForward = false;
            setTimeout(cycle, this.pauseDelay);
            return;
          }
        } else {
          if (this.charIndex > 0) {
            this.charIndex--;
          } else {
            // Reset flags for next text
            this.typingForward = true;
            this.index = (this.index + 1) % this.texts.length;
          }
        }

        // Update display parts
        if (this.charIndex <= text.length) {
          this.displayText = text.slice(0, this.charIndex);
          this.displayHighlight = '';
        } else {
          this.displayText = text;
          const hlIndex = this.charIndex - text.length;
          this.displayHighlight = highlight.slice(0, hlIndex);
        }

        // Schedule next tick
        const delay = this.typingForward ? this.typingDelay : this.typingDelay / 2;
        setTimeout(cycle, delay);
      };

      cycle();
    }
  },
  mounted() {
    this.startTyping();
  }
};
</script>

<style scoped>
.typing-cursor-blink {
  animation: blink-opacity 1s steps(1) infinite;
}
@keyframes blink-opacity {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}
</style>
