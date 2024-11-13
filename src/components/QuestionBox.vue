<template>
    <div>
      <p>{{ question.text }}</p>
      <div class="options">
        <label v-for="(option, index) in question.options" :key="index">
          <input type="radio" :value="option" v-model="selectedOption" />
          {{ option }}
        </label>
      </div>
      <button @click="submitAnswer">Enviar</button>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  
  const props = defineProps(['question', 'playerColor']);
  const emit = defineEmits(['answer']);
  
  const selectedOption = ref('');
  
  function submitAnswer() {
    emit('answer', selectedOption.value);
    selectedOption.value = ''; // Limpa a seleção
  }
  </script>
  
  <style scoped>
  .options {
    display: flex;
    flex-direction: column;
    margin: 10px 0;
  }
  button {
    margin: 5px;
  }
  </style>