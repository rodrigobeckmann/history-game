<template>
  <div class="question-container">
    <div class="question-content">
      <p class="question-text">{{ question.text }}</p>
      <div class="options">
        <label v-for="(option, index) in question.options" :key="index" class="option-label">
          <input type="radio" :value="option" v-model="selectedOption" />
          <span class="option-text">{{ option }}</span>
        </label>
      </div>
      <button @click="submitAnswer" class="submit-button" :disabled="!selectedOption">
        Responder
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps(['question']);
const emit = defineEmits(['answer']);

const selectedOption = ref('');

function submitAnswer() {
  emit('answer', selectedOption.value);
  selectedOption.value = ''; // Limpa a seleção
}
</script>

<style scoped>
.question-container {
  width: 400px;
  min-height: 400px;
  background: #333;
  border-radius: 12px;
  padding: 20px;
  color: white;
  display: flex;
  flex-direction: column;
}

.question-content {
  flex: 1;
  display: flex;
  flex-direction: column;
}

h3 {
  font-size: 1.4em;
  margin-bottom: 20px;
  color: #4a90e2;
}

.question-text {
  font-size: 1.2em;
  margin-bottom: 25px;
  line-height: 1.4;
  flex-grow: 0;
}

.options {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin: 20px 0;
  flex-grow: 1;
}

.option-label {
  display: flex;
  align-items: center;
  padding: 12px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.3s;
}

.option-label:hover {
  background: rgba(255, 255, 255, 0.2);
}

.option-label input[type="radio"] {
  display: none;
}

.option-text {
  margin-left: 15px;
  position: relative;
  padding-left: 25px;
}

.option-text::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 18px;
  height: 18px;
  border: 2px solid #4a90e2;
  border-radius: 50%;
  background: transparent;
  transition: background 0.3s;
}

input[type="radio"]:checked + .option-text::before {
  background: #4a90e2;
  box-shadow: inset 0 0 0 4px #333;
}

.submit-button {
  padding: 12px 30px;
  font-size: 1.2em;
  background: #4a90e2;
  border: none;
  border-radius: 6px;
  color: white;
  cursor: pointer;
  transition: background 0.3s;
  margin-top: auto;
}

.submit-button:hover:not(:disabled) {
  background: #357abd;
}

.submit-button:disabled {
  background: #666;
  cursor: not-allowed;
}
</style>