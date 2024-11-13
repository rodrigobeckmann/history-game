<template>
  <div v-if="isVisible" class="modal">
    <div class="modal-content">
      <h3>Pergunta para {{ playerColor }} Pawn</h3>
      <p>{{ question.text }}</p>
      <div class="options">
        <label v-for="(option, index) in question.options" :key="index">
          <input type="radio" :value="option" v-model="selectedOption" />
          {{ option }}
        </label>
      </div>
      <button @click="submitAnswer">Enviar</button>
      <button @click="closeModal">Cancelar</button>
    </div>
  </div>
</template>

<script setup>
import { ref, defineProps, defineEmits } from 'vue';

const props = defineProps(['question', 'playerColor', 'isVisible']);
const emit = defineEmits(['answer', 'close']);

const selectedOption = ref('');

function submitAnswer() {
  emit('answer', selectedOption.value);
  selectedOption.value = ''; // Limpa a seleção
}

function closeModal() {
  emit('close');
}
</script>

<style>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
}
.modal-content {
  background: #333;
  color: #fff;
  padding: 20px;
  border-radius: 8px;
  width: 300px;
  text-align: center;
}
.options {
  display: flex;
  flex-direction: column;
  margin: 10px 0;
}
button {
  margin: 5px;
}
</style>