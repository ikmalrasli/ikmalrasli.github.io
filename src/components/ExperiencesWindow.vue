<template>
  <div class="relative max-w-3xl p-4  mx-auto">
    <div class="space-y-4">
      <div v-for="(experience, idx) in experiences" :key="idx" class="relative flex" @mouseenter="hoveredCard = idx"
        @mouseleave="hoveredCard = null">
        <!-- Card -->
        <div
          class="cursor-pointer flex flex-col md:flex-row gap-4 bg-white rounded-2xl shadow-xl border border-gray-200 p-6 hover:shadow-2xl transition items-center md:items-start"
          @click="openLink(experience.link)">
          <img :src="experience.image" :alt="experience.position" class="w-28 h-28 object-cover rounded-full" />
          <div>
            <div class="flex flex-row items-center justify-between">
              <h2 class="font-bold" :class="hoveredCard === idx ? 'text-blue-600' : ''">{{
                experience.position }} | {{ experience.company }}
              </h2>
              <i class="fa fa-arrow-right mr-4 pl-4 transition-transform duration-200"
                :class="hoveredCard === idx ? 'translate-x-2 text-blue-600' : ''"></i>
            </div>

            <p class="text-xs font-semibold mb-1">{{ experience.timeframe }}</p>
            <ul v-if="Array.isArray(experience.description)"
              class="list-disc pl-5 text-gray-600 text-sm md:text-base leading-relaxed space-y-1">
              <li v-for="(item, i) in experience.description" :key="i">{{ item }}</li>
            </ul>
            <p v-else class="text-gray-600 text-sm md:text-base leading-relaxed">{{ experience.description }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ExperiencesWindow',
  data() {
    return {
      hoveredCard: null,
      experiences: [
        {
          timeframe: 'Jun 2024 - Now',
          image: new URL('../assets/experiences/control_systems_engineer.jpg', import.meta.url).href,
          position: 'Control Systems Engineer',
          company: 'Quadex Automation Sdn Bhd',
          // description: `Handle control panel design, wiring, PLC programming, and HMI development. Perform panel troubleshooting and PLC preventive maintenance. Support system integration and on-site commissioning for industrial automation projects.`,
          description: [
            'Led end-to-end control system projects for oil and gas clients.',
            'Designed and commissioned custom control panels, programming PLC logic(ladder diagram) and developing HMI graphics based on client specifications.',
            'Specialized in Allen Bradley PLCs, while also conducting program migrations from systems like Omron.',
            'Delivered comprehensive on-site services, including FAT / SAT, commissioning, troubleshooting, and preventive maintenance on various PLC systems.',
            'Collaborated with cross-functional teams to ensure timely and technically accurate project delivery.'
          ],
          link: 'https://quadex.com.my/'
        },
        {
          timeframe: 'Jan – Feb 2024',
          image: new URL('../assets/experiences/school_teacher.jpg', import.meta.url).href,
          position: 'School Teacher',
          company: 'SMK Batu Lintang',
          // description: `Taught Form 1–2 Computer Science and Geography. Helped students revise for exams, supervised during exams, and completed teaching-related paperwork and administrative tasks.`,
          description: [
            'Taught and mentored students in Computer Science and Geography for Forms 1-2.',
            'Managed all teaching - related duties, including curriculum delivery, exam supervision, and administrative tasks.',
            'Supported students in exam preparation, helping them revise by giving quizzes and build confidence by presenting topics.',
            'Gained experience in a collaborative school environment, utilizing problem - solving and teamwork to handle ad - hoc tasks and ensure smooth operations.'
          ],
          link: 'https://www.facebook.com/smkbl2020/'
        },
        {
          timeframe: 'Aug – Oct 2022',
          image: new URL('../assets/experiences/trainee.jpg', import.meta.url).href,
          position: 'Industrial Trainee',
          company: 'Sarawak Energy Berhad (SEB)',
          // description: `Interned at Sejingkat Power Corporation (210MW coal plant). Assisted in equipment checks, observed maintenance work, and learned power plant operations and DCS monitoring.`,
          description: [
            'Interned as an Industrial Trainee at Sejingkat Power Corporation, a 210MW coal-fired power plant under Sarawak Energy Berhad.',
            'Assisted in daily operations by performing weekly equipment status checks and observing various maintenance duties.',
            'Gained hands-on experience in power plant operations, including DCS monitoring and equipment maintenance.',
            'Developed key professional skills including teamwork, problem-solving, and effective communication in an industrial setting.'
          ],
          link: 'https://www.sarawakenergy.com/'
        }
      ]
    };
  },
  methods: {
    openLink(link) {
      window.open(link, '_blank');
    }
  }
};
</script>