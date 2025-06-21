<style>
.leaf {
  background: linear-gradient(180deg, rgba(37, 99, 1, 1) 0%, rgba(87, 199, 133, 1) 50%, rgba(237, 221, 83, 1) 100%);
  clip-path: polygon(50% 0%, 90% 20%, 100% 50%, 90% 80%, 50% 100%, 10% 80%, 0% 50%, 10% 20%);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  flex-shrink: 0;
}
.left {
  transform: rotate(-30deg);
  transform-origin: center;
}
.right {
  transform: rotate(30deg);
  transform-origin: center;
}
</style>


<template>
    <div class="items-center mt-5 ml-5 flex h-15 w-30 bg-white rounded-lg">
      <div class="w-6 h-12 leaf ml-4 my-auto left"></div>
      <div class="h-13 w-14 ml-15 my-auto rounded-lg bg-green-200">
        {{ leaves }}
      </div>
    </div>
</template>
<script setup>



</script>