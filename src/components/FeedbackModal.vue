<template>
  <div v-if="isVisible" :class="['feedback-modal', type]">
    {{ message }}
  </div>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps(['message', 'isVisible', 'type']);
</script>

<style scoped>
.feedback-modal {
  position: fixed;
  top: 30%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 15px 30px;
  border-radius: 8px;
  font-size: 18px;
  z-index: 1000;
  text-align: center;
  color: #fff;
}

.success {
  background-color: rgba(0, 128, 0, 0.8);
}

.error {
  background-color: rgba(255, 0, 0, 0.8);
}
</style>