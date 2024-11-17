<template>
  <div v-if="isVisible" class="justification-modal">
    <div class="modal-content">
      <h3>Resposta Incorreta</h3>
      <p>{{ justification }}</p>
      <button @click="$emit('close')">Entendi</button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  isVisible: Boolean,
  justification: String
});

defineEmits(['close']);
</script>

<style scoped>
.justification-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: #333;
  color: #fff;
  padding: 20px;
  border-radius: 8px;
  width: 400px;
  text-align: center;
}

button {
  margin-top: 15px;
  padding: 8px 16px;
  background: #4a90e2;
  border: none;
  border-radius: 4px;
  color: white;
  cursor: pointer;
}

button:hover {
  background: #357abd;
}
</style> 