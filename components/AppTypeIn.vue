<template>
  <div class="flex flex-wrap justify-center">
    <div
      v-for="(char, index) in content"
      :key="index"
      class="animate__animated animate__fadeInUp"
      :style="{
        animationDelay: `${index * 0.1}s`,
      }"
    >
      {{ char }}
    </div>
  </div>
</template>

<script lang="ts" setup>
const props = defineProps<{
  content: string;
}>();
</script>
