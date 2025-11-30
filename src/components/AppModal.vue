<template>
  <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
    <slot></slot>
  </div>
</template>

<script setup>
const props = defineProps(['show']);
const emit = defineEmits(['close']);
</script>