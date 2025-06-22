<!--
This file is the main Vue app for the left/right split layout.

CSS/Component notes:
- leaf: The leaf shape, used for the leaf counter. Gradient and shape are in main.css.
- left/right: Rotates the leaf left/right by 30deg. Use left for left-aligned, right for right-aligned leaves.
- dirt-light: Light brown background color for the right menu (see @theme in main.css).
- dirt-dark: Dark brown border color for the right menu (see @theme in main.css).
- beige: Beige background color for the main app (see @theme in main.css).
- left-frame-bg: Applies a sky-to-grass vertical gradient to the left frame. Sky is the top 80%, grass is the bottom 20%.
- cloud, cloud-shape-1/2/3: Cloud shapes and styles. See main.css for details and how to add more shapes.
- All custom colors and gradients are defined in main.css under @theme.
- Tailwind is used for layout, spacing, and utility classes throughout.
- The right menu is always visible as a sliver, and expands when toggled.
- Cloud logic is in this file; all visual styles are in main.css.

To add more custom shapes, gradients, or colors, update main.css and reference the class here.
-->


<template>
  <div class="aspect-16-9 flex flex-row">
    <!-- Left frame with sky/grass background and animated clouds -->
    <div class="flex-1 left-frame-bg relative overflow-hidden">
      <div
        v-for="cloud in clouds"
        :key="cloud.id"
        :class="['cloud', cloud.shape]"
        :style="{
          top: cloud.top + 'vw',
          left: cloud.left + 'vw',
          transition: 'none',
          transform: `translateX(${cloud.x}vw)`
        }"
      ></div>
    </div>
    <!-- Right section (always visible as a sliver on the right, height matches screen) -->
    <div
      class="fixed top-0 right-0 bg-dirt-light border-l-8 border-dirt-dark flex flex-col items-start justify-start p-8 z-10 transition-all duration-300 ease-in-out"
      :style="menuStyle"
    >
      <!-- Toggle button -->
      <button
        class="absolute left-0 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-dirt-dark text-white rounded-full px-2 py-2 shadow-lg border-4 border-white z-20 focus:outline-none focus:ring-2 focus:ring-dirt-light cursor-pointer"
        @click="toggleMenu"
        aria-label="Toggle menu"
        style="box-shadow: 0 2px 8px rgba(0,0,0,0.25);"
      >
        <!-- emoji config, stole off a forum, idk how to switch the emojis used -->
        <span v-if="!menuOpen">&#x25C0;</span>
        <span v-else>&#x25B6;</span>
      </button>
      <div v-show="menuOpen" class="flex flex-row items-start w-full">
        <!-- Leaves -->
        <div class="flex items-center h-15 w-30 bg-white rounded-lg">
          <div class="w-6 h-12 leaf ml-4 my-auto left"></div>
          <div class="h-13 w-14 ml-5 my-auto rounded-lg bg-green-200 flex flex-col items-center justify-center">
            <span class="font-bold text-2xl">{{ leaves }}</span>
          </div>
        </div>

        <!-- temporary for js test -->
        <div class="flex flex-row items-center ml-4">
          <button class="ml-2 bg-white text-black px-4 py-2 rounded shadow" @click="addLeaf">+</button>
          <button class="ml-2 bg-white text-black px-4 py-2 rounded shadow" @click="removeLeaf">-</button>
          <button class="ml-2 bg-white text-black px-4 py-2 rounded shadow" @click="resetLeaves">Reset</button>
        </div>
      </div>
    </div>
  </div>
</template>


<!--
shoulda added notes for the js but most of it is self explanatory i guess
-->

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from "vue";

// --- State Variables ---
const leaves = ref(0);
const menuOpen = ref(false);
const menuPeek = 32; // px, always visible sliver
const cloudShapes = ["cloud-shape-1", "cloud-shape-2", "cloud-shape-3"];
let cloudIdCounter = 0;
const clouds = ref([]);
let rafId;
let spawnTimeoutId;
let thickness = ref(0.5); // Controls cloud spawn rate

// --- UI Functions ---
const menuStyle = computed(() => {
  return menuOpen.value
    ? `height: 100vh; width: 98vw; min-width: 300px; max-width: 100vw; border-top-left-radius: 0.75rem; border-bottom-left-radius: 0.75rem;`
    : `height: 100vh; width: ${menuPeek}px; min-width: ${menuPeek}px; max-width: ${menuPeek}px; border-top-left-radius: 0.75rem; border-bottom-left-radius: 0.75rem;`;
});
function toggleMenu() { menuOpen.value = !menuOpen.value; }
function addLeaf() { leaves.value++; }
function removeLeaf() { if (leaves.value > 0) leaves.value--; }
function resetLeaves() { leaves.value = 0; }

// --- Cloud Logic ---
function randomCloud(offset = 0, topOverride = null) {
  return {
    id: cloudIdCounter++,
    shape: cloudShapes[Math.floor(Math.random() * cloudShapes.length)],
    top: topOverride !== null ? topOverride : Math.random() * 12 + 1, // 1vw to 13vw from top
    left: -Math.random() * 10 - 10 + offset, // offset for initial spacing
    x: 0,
    speed: Math.random() * 0.03 + 0.015
  };
}
function spawnCloud(offset = 0, topOverride = null) {
  clouds.value.push(randomCloud(offset, topOverride));
}
function animateClouds() {
  clouds.value.forEach(cloud => {
    cloud.x += cloud.speed;
  });
  clouds.value = clouds.value.filter(cloud => cloud.left + cloud.x <= 110);
  rafId = requestAnimationFrame(animateClouds);
}
function spawnCloudLoop() {
  spawnCloud();
  spawnTimeoutId = setTimeout(spawnCloudLoop, 3000 / thickness.value);
}

// --- Lifecycle ---
onMounted(() => {
  for (let i = 0; i < 3; i++) {
    setTimeout(() => {
      const top = 1 + (12 / 2) * i + Math.random() * 2 - 1;
      spawnCloud(-i * 60, top);
    }, i * 1200);
  }
  rafId = requestAnimationFrame(animateClouds);
  spawnTimeoutId = setTimeout(spawnCloudLoop, 1200 * 3);
});
onBeforeUnmount(() => {
  cancelAnimationFrame(rafId);
  clearTimeout(spawnTimeoutId);
});
</script>

<style>
/* Add your cloud animations here */
.cloud {
  position: absolute;
  width: 15vw;
  height: 8vw;
  background: white;
  border-radius: 50%;
  opacity: 0.9;
  pointer-events: none;
}

.cloud-shape-1 {
  /* Specific styles for cloud shape 1 */
}

.cloud-shape-2 {
  /* Specific styles for cloud shape 2 */
}

.cloud-shape-3 {
  /* Specific styles for cloud shape 3 */
}
</style>