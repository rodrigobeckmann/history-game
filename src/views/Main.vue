<template>
  <div class="main-wrapper">
    <div v-if="!isGameStarted" class="setup">
      <h2>Configuração dos Jogadores</h2>
      <div>
        <label>Nome do Jogador 1:</label>
        <input v-model="playerNames[0]" placeholder="Nome do Jogador 1" />
      </div>
      <div>
        <label>Nome do Jogador 2:</label>
        <input v-model="playerNames[1]" placeholder="Nome do Jogador 2" />
      </div>
      <button @click="startGame">Iniciar Jogo</button>
    </div>

    <GamePage v-else :playerNames="playerNames" />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import GamePage from '../components/GamePage.vue';

const playerNames = ref(["", ""]);
const isGameStarted = ref(false);

function startGame() {
  if (playerNames.value[0] && playerNames.value[1]) {
    isGameStarted.value = true;
  } else {
    alert("Por favor, insira o nome de ambos os jogadores.");
  }
}
</script>

<style>
.main-wrapper {
  text-align: center;
}

.setup {
  margin-top: 20px;
}

button {
  margin: 5px;
}
</style>