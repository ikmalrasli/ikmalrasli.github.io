<template>
  <div class="max-w-3xl mx-auto p-2 md:p-4 md:p-8 text-sm md:text-base">
    <div class="grid grid-cols-1 md:grid-cols-2 gap-2 md:gap-8">
      <!-- Contact Info Card -->
      <div
        class="bg-white rounded-2xl shadow-xl border border-gray-200 p-6 flex flex-col justify-between text-sm md:text-base">
        <div>
          <div class="flex items-center justify-center relative gap-2 mb-4 text-gray-700">
            <i class="absolute left-0 top-1/2 transform -translate-y-1/2 fa fa-network-wired text-2xl"></i>
            <span class="font-bold text-lg">My Socials</span>
          </div>
          <p class="text-gray-600 mb-4">Feel free to reach out if you have any questions or want to work together.</p>
          <div class="space-y-2">
            <a href="mailto:ikmalrasli@gmail.com"
              class="flex items-center text-gray-700 hover:text-blue-600 hover:underline">
              <i class="fa fa-envelope mr-2"></i>ikmalrasli@gmail.com
            </a>
            <a href="https://www.github.com/ikmalrasli" target="_blank"
              class="flex items-center text-gray-700 hover:text-blue-600 hover:underline">
              <i class="fa-brands fa-github mr-2"></i> My Github
            </a>
            <a href="https://www.linkedin.com/in/ikmal-rasli-4b17aa264/" target="_blank"
              class="flex items-center text-gray-700 hover:text-blue-600 hover:underline">
              <i class="fa-brands fa-linkedin mr-2"></i> My LinkedIn
            </a>
          </div>
        </div>
      </div>
      <!-- Message Form Card -->
      <div class="bg-white rounded-2xl shadow-xl border border-gray-200 p-6 text-sm md:text-base">
        <div class="flex items-center justify-center relative gap-2 mb-4 text-gray-700 text-base md:text-lg">
          <i class="absolute left-0 top-1/2 transform -translate-y-1/2 fa fa-paper-plane text-2xl"></i>
          <span class="font-bold">Send me a message!</span>
        </div>
        <form class="space-y-4" @submit.prevent="submitForm">
          <div>
            <label class="block text-xs font-bold text-gray-600 mb-1">Name <span class="text-red-500">*</span></label>
            <input type="text" v-model="form.name" required
              class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-400" />
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-600 mb-1">Email <span class="text-red-500">*</span></label>
            <input type="email" v-model="form.email" required
              class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-400" />
            <div v-if="form.email && !isValidEmail" class="text-xs text-red-500 mt-1">
              Please enter a valid email address.
            </div>
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-600 mb-1">Message <span
                class="text-red-500">*</span></label>
            <textarea rows="3" v-model="form.message" required
              class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-400"></textarea>
          </div>
          <button type="submit" :disabled="loading || !form.name || !form.email || !form.message"
            class="w-full py-2 rounded-lg bg-blue-600 text-white font-bold text-sm shadow hover:bg-blue-700 transition disabled:opacity-60 disabled:cursor-not-allowed">
            {{ loading ? 'Sending...' : 'Send Message' }}
          </button>
          <div v-if="feedback" :class="feedbackType === 'success' ? 'text-green-600' : 'text-red-600'"
            class="text-sm text-center font-semibold mt-2">{{ feedback }}</div>
        </form>
      </div>
    </div>
  </div>

</template>

<script setup>
import { ref, computed } from 'vue';
import emailjs from 'emailjs-com';

const form = ref({ name: '', email: '', message: '' });
const loading = ref(false);
const feedback = ref('');
const feedbackType = ref('');
const isValidEmail = computed(() =>
  /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.value.email)
);
const canSubmit = computed(() =>
  form.value.name && form.value.email && form.value.message && isValidEmail.value && !loading.value
);

const submitForm = async () => {
  feedback.value = '';
  feedbackType.value = '';
  loading.value = true;
  try {
    const result = await emailjs.send(
      'service_i0hr2j7',
      'template_tiddewg',
      {
        name: form.value.name,
        email: form.value.email,
        message: form.value.message,
      },
      'ST3uYZsD16DepEOR5'
    );
    form.value = { name: '', email: '', message: '' };
    feedback.value = 'Message sent successfully!';
    feedbackType.value = 'success';
  } catch (e) {
    feedback.value = 'Failed to send message. Please try again later.';
    feedbackType.value = 'error';
  } finally {
    loading.value = false;
  }
};
</script>