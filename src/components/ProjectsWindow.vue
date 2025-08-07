<template>
  <div class="max-w-5xl mx-auto p-4 ">
    <div class="flex flex-wrap gap-4 mb-4 justify-center overflow-x-auto">
      <button @click="selectedCategory = 'all'"
        :class="{ 'bg-blue-500 text-white': selectedCategory === 'all', 'bg-gray-200 text-gray-700 hover:cursor-pointer hover:bg-blue-100 hover:text-blue-700': selectedCategory !== 'all' }"
        class="rounded-full px-4 py-2 text-sm font-semibold transition-colors duration-200 ">
        All
      </button>
      <button @click="selectedCategory = 'control system'"
        :class="{ 'bg-blue-500 text-white': selectedCategory === 'control system', 'bg-gray-200 text-gray-700 hover:cursor-pointer hover:bg-blue-100 hover:text-blue-700': selectedCategory !== 'control system' }"
        class="rounded-full px-4 py-2 text-sm font-semibold transition-colors duration-200 ">
        Control System
      </button>
      <button @click="selectedCategory = 'software development'"
        :class="{ 'bg-blue-500 text-white': selectedCategory === 'software development', 'bg-gray-200 text-gray-700 hover:cursor-pointer hover:bg-blue-100 hover:text-blue-700': selectedCategory !== 'software development' }"
        class="rounded-full px-4 py-2 text-sm font-semibold transition-colors duration-200">
        Software Development
      </button>
      <button @click="selectedCategory = 'university'"
        :class="{ 'bg-blue-500 text-white': selectedCategory === 'university', 'bg-gray-200 text-gray-700 hover:cursor-pointer hover:bg-blue-100 hover:text-blue-700': selectedCategory !== 'university' }"
        class="rounded-full px-4 py-2 text-sm font-semibold transition-colors duration-200">
        University
      </button>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
      <router-link v-for="project in filteredProjects" :key="project.id"
        :to="{ name: 'ProjectDetails', params: { id: project.id } }"
        class="group bg-white rounded-2xl shadow-xl border border-gray-200 hover:shadow-2xl transition flex flex-col overflow-hidden cursor-pointer">
        <div class="relative aspect-video flex items-center justify-center border-b border-gray-100">
          <img :src="project.image" :alt="project.title" class="object-cover w-full h-full rounded-t-2xl" />
        </div>
        <div class="flex-1 flex flex-col p-4">
          <div class="flex items-center justify-between mb-2">
            <h3 class="text-lg font-bold transition-colors duration-200 group-hover:text-blue-600">{{ project.title }}
            </h3>
          </div>
          <p class="text-sm text-gray-600 mb-4 flex-1">
            {{ project.description }}
          </p>
          <div class="flex flex-wrap gap-2 mb-2">
            <span v-for="tag in project.tags" :key="tag"
              class="px-2 py-1 rounded-full bg-gray-100 text-gray-700 text-xs font-semibold transition-colors duration-200 group-hover:bg-blue-100 group-hover:text-blue-700">{{
              tag }}</span>
          </div>
        </div>
      </router-link>
    </div>
  </div>
</template>

<script>
import { computed, ref } from 'vue';

export default {
  name: 'ProjectsWindow',
  setup() {
    const selectedCategory = ref('all');

    const projects = [
      {
        id: 1,
        title: 'HVAC Systems PLC and HMI Upgrade for PCSB PMA Resak',
        tag: '.PLC',
        category: 'control system',
        description: 'PLC and HMI upgrade for 8 HVAC systems, including program migration from Omron PLC to Allen Bradley PLC (CompactLogix).',
        image: new URL('../assets/projects/pcsb_resak_hvac.jpg', import.meta.url).href,
        isNew: true,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'PLC and HMI upgrade for 8 HVAC Systems, including additional EWS. Program migration from Omron PLC to Allen Bradley PLC (CompactLogix). Developed graphics for HMI (Panelview Plus 7 Standard). Performed FAT, SAT and commissioning for all 8 systems.',
        photos: ['https://via.placeholder.com/600x400/0000FF/FFFFFF?text=HVAC+Photo+1', 'https://via.placeholder.com/600x400/0000FF/FFFFFF?text=HVAC+Photo+2'],
        tags: ['PLC', 'HMI', 'Allen Bradley', 'Omron'],
      },
      {
        id: 2,
        title: 'Nitrogen Generation System for Shell F6 Vlap',
        tag: '.PLC',
        category: 'control system',
        description: 'Designed and pre-commissioned the local control panel for a nitrogen generation package using Allen Bradley PLC (ControlLogix).',
        image: new URL('../assets/projects/ISS_n2_vlap.jpg', import.meta.url).href,
        isNew: false,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'Designed, set up, and pre-commissioned the local control panel for a nitrogen generation package, performed FAT and SAT to ensure seamless integration and optimal performance of the PLC-based control system. Designed control panel (System Architecture, General Arrangement, Wiring Diagram). Developed program for Allen Bradley PLC (ControlLogix) and graphics for HMI (Panelview Plus 7 Performance). Performed FAT and SAT.',
        photos: ['https://via.placeholder.com/600x400/0000FF/FFFFFF?text=Nitrogen+Photo+1', 'https://via.placeholder.com/600x400/0000FF/FFFFFF?text=Nitrogen+Photo+2'],
        tags: ['PLC', 'HMI', 'Allen Bradley', 'Control Panel Design'],
      },
      {
        id: 3,
        title: 'COG Hogback System for AirLiquide',
        tag: '.PLC',
        category: 'control system',
        description: 'Developed and implemented the system control panel for a helium recovery package with Allen Bradley PLC (CompactLogix).',
        image: new URL('../assets/projects/AL_hogback.jpg', import.meta.url).href,
        isNew: false,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'Developed and implemented the system control panel for a helium recovery package, configuring control strategies, performing FAT, and ensuring precise system integration for efficient recovery and operation. Designed control panel (System Architecture, General Arrangement, Wiring Diagram). Developed program for Allen Bradley PLC (CompactLogix) and graphics for an Industrial PC.',
        photos: ['https://via.placeholder.com/600x400/0000FF/FFFFFF?text=Helium+Photo+1', 'https://via.placeholder.com/600x400/0000FF/FFFFFF?text=Helium+Photo+2'],
        tags: ['PLC', 'HMI', 'Allen Bradley', 'Control Panel Design'],
      },
      {
        id: 4,
        title: 'Annual PM for Allen Bradley PLC Systems at PTTEP',
        tag: '.PM',
        category: 'control system',
        description: 'Performed annual preventive maintenance for 27 Allen Bradley PLC systems, including visual inspection, system health checks, and program backup.',
        image: new URL('../assets/projects/pm_pttep_serendah.jpg', import.meta.url).href,
        isNew: false,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'Performed visual inspection, check system health, backup HMI and PLC program, troubleshoot/debug existing program and backup latest program, perform fault rectification for 27 systems across various satellite platforms.',
        photos: ['https://via.placeholder.com/600x400/0000FF/FFFFFF?text=PTTEP+PM+Photo+1'],
        tags: ['Preventive Maintenance', 'PLC', 'HMI', 'Allen Bradley'],
      },
      {
        id: 5,
        title: 'PM and site survey for Siemens and Mitsubishi PLC Systems',
        tag: '.PM',
        category: 'control system',
        description: 'Conducted preventive maintenance and site surveys for 2 Siemens and Mitsubishi PLC systems at Jadestone Belumut Platform.',
        image: new URL('../assets/projects/pm_jse_belumut.jpg', import.meta.url).href,
        isNew: false,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'Performed visual inspection, check system health, backup HMI and PLC program, troubleshoot/debug existing program and backup latest program, perform fault rectification for 2 systems.',
        photos: ['https://via.placeholder.com/600x400/0000FF/FFFFFF?text=Jadestone+PM+Photo+1'],
        tags: ['Preventive Maintenance', 'PLC', 'HMI', 'Siemens'],
      },
      {
        id: 6,
        title: 'Logic Diagram for Ethylene Liquefaction Package',
        tag: '.ENGINEERING',
        category: 'control system',
        description: 'Developed the logic diagram for the control system of an ethylene liquefaction package for INEOS, ensuring safe and efficient operation.',
        image: new URL('../assets/projects/rei_logic_diagram.png', import.meta.url).href,
        isNew: false,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'Developed the logic diagram for the control system of an ethylene liquefaction package, integrating process variables and safety interlocks to ensure efficient and safe operation.',
        photos: ['https://via.placeholder.com/600x400/0000FF/FFFFFF?text=INEOS+Logic+Diagram'],
        tags: ['Control System Design', 'Logic Diagram', 'P&ID'],
      },
      {
        id: 7,
        title: 'BitByBit',
        tag: '.WEBAPP',
        category: 'software development',
        description: 'A web-app habit tracker built with Vue.js that helps users track habits, view statistics, and receive push notifications.',
        image: new URL('../assets/projects/bitbybit.png', import.meta.url).href,
        isNew: true,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'A web-app habit tracker built with Vue.js, that helps users build and track habits, displays performance statistics, and sends push notifications to keep users on track.',
        photos: ['https://via.placeholder.com/600x400/008000/FFFFFF?text=BitByBit+Screenshot+1'],
        tags: ['Vue.js', 'Web App', 'Frontend', 'PWA'],
      },
      {
        id: 8,
        title: 'Extract Exam Questions',
        tag: '.WEBTOOL',
        category: 'software development',
        description: 'A web tool that extracts questions, diagrams, and tables from exam PDFs into formatted DOCX files using FastAPI, Gemini API, and Vue.js.',
        image: new URL('../assets/projects/extract_exam_questions.png', import.meta.url).href,
        isNew: true,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'A web tool developed with FastAPI, Gemini API, and Vue.js that extracts questions, diagrams, and tables from exam PDFs (rasterized or text-based) into clean, formatted DOCX files—saving teachers hours of manual work.',
        photos: ['https://via.placeholder.com/600x400/008000/FFFFFF?text=Exam+Extractor+Screenshot+1'],
        tags: ['FastAPI', 'Gemini API', 'PDF Processing', 'Web Tool'],
      },
      {
        id: 9,
        title: 'Automatic Fish Feeder for Aquaponic Systems',
        tag: '.IOT',
        category: 'university',
        description: 'Developed an automatic fish feeder that dispenses food based on fish count and age, optimizing aquaponic system feeding cycles.',
        image: new URL('../assets/projects/fish_feeder.jpg', import.meta.url).href,
        isNew: false,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'Developed an automatic fish feeder that dispenses appropriate amounts of food based on fish number and age.',
        photos: ['https://via.placeholder.com/600x400/800080/FFFFFF?text=Fish+Feeder+Photo+1'],
        tags: ['IoT', 'Arduino', 'Aquaponics', 'Mechatronics'],
      },
      {
        id: 10,
        title: 'RFID Home Security System with License Plate Recognition',
        tag: '.ML',
        category: 'university',
        description: 'An automatic gate security system using RFID and machine learning-based license plate recognition for vehicle access control.',
        image: new URL('../assets/projects/home_security.png', import.meta.url).href,
        isNew: false,
        codeLink: '#',
        demoLink: '#',
        fullDescription: 'Automatic gate that opens by detecting RFID and license plate of the house owner vehicle through image processing and machine learning process.',
        photos: ['https://via.placeholder.com/600x400/800080/FFFFFF?text=Security+System+Photo+1'],
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
    };
  },
};
</script>