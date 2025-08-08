<template>
  <div class="space-y-2 md:space-y-4 p-2 md:p-4 w-full max-w-3xl mx-auto text-gray-700 text-sm md:text-base">
    <!-- Profile Card -->
    <div
      class="flex flex-col items-center md:items-start md:flex-row gap-4 bg-white rounded-3xl shadow-lg p-6 border border-gray-200 text-sm md:text-base">
      <div
        class="flex-shrink-0 w-24 h-24 rounded-full overflow-hidden border-4 border-blue-400 shadow-lg bg-gradient-to-br from-blue-100 to-white">
        <img src="../assets/about/pfp.jpeg" class="w-full h-full object-cover" alt="Profile picture" />
      </div>
      <div class="flex-1 min-w-0">
        <h2 class="text-xl md:text-3xl font-bold mb-1">IKMAL RASLI</h2>
        <p class="mb-2 font-semibold">Control Systems Engineer | Software Developer
        </p>
        <ul class="list-disc pl-5 text-gray-600 leading-relaxed space-y-1">
          <li>Control Systems Engineer with hands-on experience in the oil and gas industry.</li>
          <li>Adept at turning client needs into technical solutions, with expertise in PLC programming, HMI
            development, and system integration.</li>
          <li>Passionate about software development, building scalable applications with Python and JavaScript.</li>
          <li>Continuously improves skills through personal projects.</li>
        </ul>
      </div>
    </div>

    <!-- Education Card -->
    <div
      class="bg-white rounded-2xl shadow-lg border border-gray-200 p-6 flex flex-col md:flex-row gap-6 items-center text-sm md:text-base">
      <img src="../assets/about/UM_logo.jpg" alt="University of Malaya Logo" class="w-20 h-20" />
      <div class="flex-1 min-w-0">
        <h2 class="text-lg md:text-xl font-bold mb-1">B.Eng (Hons) in Mechanical Engineering</h2>
        <h3 class="font-semibold text-gray-700 mb-1">University of Malaya</h3>
        <p class="text-gray-500 mb-1">Sep 2019 - Jul 2023</p>
        <div class="text-gray-500">CGPA: 3.63/4.00</div>
      </div>
    </div>

    <!-- Skills Card -->
    <div class="bg-white rounded-2xl shadow-lg border border-gray-200 p-6 text-sm md:text-base">
      <h2 class="text-xl md:text-2xl font-bold mb-4">SKILLS</h2>
      <div class="space-y-6 text-sm">
        <div>
          <div class="font-semibold text-gray-500 mb-1">CONTROL SYSTEMS/ENGINEERING</div>
          <div class="flex flex-wrap gap-2">
            <span
              v-for="skill in ['PLC Programming', 'HMI Design', 'Control Panel Design & Wiring', 'Control Panel Troubleshooting', 'AutoCAD', 'ANSYS Fluent', 'MATLAB', 'SolidWorks']"
              :key="skill" class="border px-3 py-1 rounded-full font-medium shadow-sm transition">
              {{ skill }}
            </span>
          </div>
        </div>
        <div>
          <div class="font-semibold text-gray-500 mb-1">SOFTWARE DEVELOPMENT</div>
          <div class="flex flex-wrap gap-2">
            <span v-for="skill in ['Python', 'JAVA', 'C++', 'Vue', 'Angular', 'HTML', 'CSS', 'JavaScript']" :key="skill"
              class="border px-3 py-1 rounded-full font-medium shadow-sm transition">
              {{ skill }}
            </span>
          </div>
        </div>
        <div>
          <div class="font-semibold text-gray-500 mb-1">LANGUAGES</div>
          <div class="flex flex-wrap gap-2">
            <span
              v-for="skill in ['Malay (Native)', 'English (Proficient)', 'Mandarin (Conversational)', 'Japanese (Basic)']"
              :key="skill" class="border px-3 py-1 rounded-full font-medium shadow-sm transition">
              {{ skill }}
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>