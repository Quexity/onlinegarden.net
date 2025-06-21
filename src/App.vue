<!--
Theres a few things in main.css for stuff i couldnt do in tailwind

leaf: the leaf shape with everything, might make more leaves and options for gradients to make it customizable later on
left: makes it angled 30 degrees to the left (!!! ALIGN FROM THE LEFT SIDE OF LEAF ON STALK !!!)
right: makes it angled 30 degrees to the right (!!! ALIGN FROM THE RIGHT SIDE OF LEAF ON STALK !!!)

dirt-light: the light brown background color
dirt-dark: the dark brown border color
beige: the beige background color that i liked

-->


<template>
  <!--
  Right section (probably should have its own class)
  -->
  <div
    class="fixed top-0 right-0 h-screen bg-dirt-light border-l-8 border-dirt-dark flex flex-col items-start justify-start p-8 z-10 transition-all duration-300 ease-in-out"
    :style="menuStyle"
  >
    <!-- Toggle button (i also shouldve made a class for this :sob:)-->
    <button
      class="absolute left-0 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-dirt-dark text-white rounded-full px-2 py-2 shadow-lg border-4 border-white z-20 focus:outline-none focus:ring-2 focus:ring-dirt-light"
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
</template>


<!--
shoulda added notes for the js but most of it is self explanatory i guess
-->

<script setup>
import { ref, computed } from "vue";

const leaves = ref(0);
const menuOpen = ref(false);

const menuPeek = 32;

const menuStyle = computed(() => {
  return menuOpen.value
    ? `width: 98vw; min-width: 300px; max-width: 100vw; border-top-left-radius: 0.75rem; border-bottom-left-radius: 0.75rem;`
    : `width: ${menuPeek}px; min-width: ${menuPeek}px; max-width: ${menuPeek}px; border-top-left-radius: 0.75rem; border-bottom-left-radius: 0.75rem; overflow: hidden;`;
});

function toggleMenu() {
  menuOpen.value = !menuOpen.value;
}

function addLeaf() {
  leaves.value++;
}

function removeLeaf() {
  if (leaves.value > 0) {
    leaves.value--;
  }
}

function resetLeaves() {
  leaves.value = 0;
}
</script>