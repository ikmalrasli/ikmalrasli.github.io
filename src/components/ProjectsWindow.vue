<template>
  <div class="max-w-5xl mx-auto p-2 md:p-4 text-sm md:text-base ">
    <div class="flex flex-nowrap gap-2 mb-4 justify-center overflow-x-auto w-full text-xs">
      <button @click="selectedCategory = 'all'"
        :class="{ 'bg-blue-500 text-white': selectedCategory === 'all', 'bg-gray-200 text-gray-700 hover:cursor-pointer hover:bg-blue-100 hover:text-blue-700': selectedCategory !== 'all' }"
        class="rounded-full px-4 py-2 font-semibold transition-colors duration-200">
        All
      </button>
      <button @click="selectedCategory = 'control system'"
        :class="{ 'bg-blue-500 text-white': selectedCategory === 'control system', 'bg-gray-200 text-gray-700 hover:cursor-pointer hover:bg-blue-100 hover:text-blue-700': selectedCategory !== 'control system' }"
        class="rounded-full px-4 py-2 font-semibold transition-colors duration-200">
        Control System
      </button>
      <button @click="selectedCategory = 'software development'"
        :class="{ 'bg-blue-500 text-white': selectedCategory === 'software development', 'bg-gray-200 text-gray-700 hover:cursor-pointer hover:bg-blue-100 hover:text-blue-700': selectedCategory !== 'software development' }"
        class="rounded-full px-4 py-2 font-semibold transition-colors duration-200">
        Software Development
      </button>
      <button @click="selectedCategory = 'university'"
        :class="{ 'bg-blue-500 text-white': selectedCategory === 'university', 'bg-gray-200 text-gray-700 hover:cursor-pointer hover:bg-blue-100 hover:text-blue-700': selectedCategory !== 'university' }"
        class="rounded-full px-4 py-2 font-semibold transition-colors duration-200">
        University
      </button>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-2 md:gap-4 text-sm md:text-base">
      <div v-for="project in filteredProjects" :key="project.id" @click="openProjectLink(project.link)"
        class="group bg-white rounded-2xl shadow-xl border border-gray-200 hover:shadow-2xl transition flex flex-col overflow-hidden cursor-pointer">
        <div class="relative aspect-video flex items-center justify-center border-b border-gray-100">
          <img :src="project.image" :alt="project.title" class="object-cover w-full h-full rounded-t-2xl" />
        </div>
        <div class="flex-1 flex flex-col p-4">
          <div class="flex items-center justify-between mb-2">
            <h3 class="font-bold text-base md:text-lg transition-colors duration-200 group-hover:text-blue-600">{{
              project.title }}
            </h3>
          </div>
          <p class="text-gray-600 mb-4 flex-1">
            {{ project.description }}
          </p>
          <div class="flex flex-wrap gap-2 mb-2">
            <span v-for="tag in project.tags" :key="tag"
              class="px-2 py-1 rounded-full bg-gray-100 text-gray-700 text-xs font-semibold transition-colors duration-200 group-hover:bg-blue-100 group-hover:text-blue-700">{{
                tag }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { computed, ref } from 'vue';

export default {
  name: 'ProjectsWindow',
  setup() {
    const selectedCategory = ref('all');

    function openProjectLink(link) {
      if (link && link !== '#') {
        window.open(link, '_blank');
      }
    }

    const projects = [
      {
        id: 1,
        title: 'HVAC Systems PLC and HMI Upgrade for PCSB PMA Resak',
        category: 'control system',
        description: 'PLC and HMI upgrade for 8 HVAC systems, including program migration from Omron PLC to Allen Bradley PLC (CompactLogix).',
        image: new URL('../assets/projects/pcsb_resak_hvac.jpg', import.meta.url).href,
        link: '#',
        tags: ['PLC', 'HMI', 'Allen Bradley', 'Omron'],
      },
      {
        id: 2,
        title: 'Nitrogen Generation System for Shell F6 Vlap',
        category: 'control system',
        description: 'Designed and pre-commissioned the local control panel for a nitrogen generation package using Allen Bradley PLC (ControlLogix).',
        image: new URL('../assets/projects/ISS_n2_vlap.jpg', import.meta.url).href,
        link: '#',
        tags: ['PLC', 'HMI', 'Allen Bradley', 'Control Panel Design'],
      },
      {
        id: 3,
        title: 'COG Hogback System for AirLiquide',
        tag: '.PLC',
        category: 'control system',
        description: 'Developed and implemented the system control panel for a helium recovery package with Allen Bradley PLC (CompactLogix).',
        image: new URL('../assets/projects/AL_hogback.jpg', import.meta.url).href,
        link: '#',
        tags: ['PLC', 'HMI', 'Allen Bradley', 'Control Panel Design'],
      },
      {
        id: 4,
        title: 'Annual PM for Allen Bradley PLC Systems at PTTEP',
        tag: '.PM',
        category: 'control system',
        description: 'Performed annual preventive maintenance for 27 Allen Bradley PLC systems, including visual inspection, system health checks, and program backup.',
        image: new URL('../assets/projects/pm_pttep_serendah.jpg', import.meta.url).href,
        link: '#',
        tags: ['Preventive Maintenance', 'PLC', 'HMI', 'Allen Bradley'],
      },
      {
        id: 5,
        title: 'PM and site survey for Siemens and Mitsubishi PLC Systems',
        tag: '.PM',
        category: 'control system',
        description: 'Conducted preventive maintenance and site surveys for 2 Siemens and Mitsubishi PLC systems at Jadestone Belumut Platform.',
        image: new URL('../assets/projects/pm_jse_belumut.jpg', import.meta.url).href,
        link: '#',
        tags: ['Preventive Maintenance', 'PLC', 'HMI', 'Siemens'],
      },
      {
        id: 6,
        title: 'Logic Diagram for Ethylene Liquefaction Package',
        tag: '.ENGINEERING',
        category: 'control system',
        description: 'Developed the logic diagram for the control system of an ethylene liquefaction package for INEOS, ensuring safe and efficient operation.',
        image: new URL('../assets/projects/rei_logic_diagram.png', import.meta.url).href,
        link: '#',
        tags: ['Control System Design', 'Logic Diagram', 'P&ID'],
      },
      {
        id: 7,
        title: 'BitByBit',
        tag: '.WEBAPP',
        category: 'software development',
        description: 'A web-app habit tracker built with Vue.js that helps users track habits, view statistics, and receive push notifications.',
        image: new URL('../assets/projects/bitbybit.png', import.meta.url).href,
        link: 'https://bitbybit-5afe4.web.app/',
        tags: ['Vue.js', 'Web App', 'Frontend', 'PWA'],
      },
      {
        id: 8,
        title: 'Extract Exam Questions',
        tag: '.WEBTOOL',
        category: 'software development',
        description: 'A web tool that extracts questions, diagrams, and tables from exam PDFs into formatted DOCX files using FastAPI, Gemini API, and Vue.js.',
        image: new URL('../assets/projects/extract_exam_questions.png', import.meta.url).href,
        link: 'https://github.com/ikmalrasli/extract-exam-questions',
        tags: ['FastAPI', 'Gemini API', 'PDF Processing', 'Web Tool'],
      },
      {
        id: 9,
        title: 'Automatic Fish Feeder for Aquaponic Systems',
        tag: '.IOT',
        category: 'university',
        description: 'Developed an automatic fish feeder that dispenses food based on fish count and age, optimizing aquaponic system feeding cycles.',
        image: new URL('../assets/projects/fish_feeder.jpg', import.meta.url).href,
        link: 'https://github.com/ikmalrasli/fish-feeder-pi',
        tags: ['IoT', 'Arduino', 'Aquaponics', 'Mechatronics'],
      },
      {
        id: 10,
        title: 'RFID Home Security System with License Plate Recognition',
        tag: '.ML',
        category: 'university',
        description: 'An automatic gate security system using RFID and machine learning-based license plate recognition for vehicle access control.',
        image: new URL('../assets/projects/home_security.png', import.meta.url).href,
        link: '#',
        tags: ['Machine Learning', 'RFID', 'Computer Vision'],
      },
    ];

    const filteredProjects = computed(() => {
      if (selectedCategory.value === 'all') {
        return projects;
      }
      return projects.filter(project => project.category === selectedCategory.value);
    });

    return {
      selectedCategory,
      filteredProjects,
      openProjectLink,
    };
  },
};
</script>