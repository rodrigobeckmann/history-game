<template>
  <div v-if="isVisible" class="victory-modal">
    <div class="modal-content">
      <h2>🎉 Parabéns! 🎉</h2>
      <h3>{{ playerName }} venceu o jogo!</h3>
      <button @click="$emit('restart')">Jogar Novamente</button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  isVisible: Boolean,
  playerName: String
});

defineEmits(['restart']);
</script>

<style scoped>
.victory-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: #333;
  color: #fff;
  padding: 30px;
  border-radius: 8px;
  text-align: center;
  animation: scale-in 0.5s ease-out;
}

@keyframes scale-in {
  from { transform: scale(0); }
  to { transform: scale(1); }
}

h2 {
  font-size: 2em;
  margin-bottom: 20px;
}

button {
  margin-top: 20px;
  padding: 12px 24px;
  font-size: 1.2em;
  background: #4a90e2;
  border: none;
  border-radius: 4px;
  color: white;
  cursor: pointer;
  transition: background 0.3s;
}

button:hover {
  background: #357abd;
}
</style> 