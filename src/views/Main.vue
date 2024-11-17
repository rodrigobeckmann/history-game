<template>
  <div class="main-wrapper">
    <div v-if="!isGameStarted" class="setup">
      <h2>Configuração dos Jogadores</h2>
      
      <div class="player-setup">
        <div class="player-input red">
          <div class="color-indicator"></div>
          <div class="input-group">
            <label>Jogador Vermelho:</label>
            <input v-model="playerNames[0]" placeholder="Digite o nome" />
          </div>
        </div>

        <div class="player-input blue">
          <div class="color-indicator"></div>
          <div class="input-group">
            <label>Jogador Azul:</label>
            <input v-model="playerNames[1]" placeholder="Digite o nome" />
          </div>
        </div>
      </div>

      <button @click="startGame" class="start-button">Iniciar Jogo</button>
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

<style scoped>
.main-wrapper {
  text-align: center;
  padding: 20px;
}

.setup {
  max-width: 600px;
  margin: 40px auto;
  padding: 30px;
  background: #333;
  border-radius: 12px;
  color: white;
}

h2 {
  margin-bottom: 30px;
  font-size: 2em;
}

.player-setup {
  display: flex;
  flex-direction: column;
  gap: 20px;
  margin-bottom: 30px;
}

.player-input {
  display: flex;
  align-items: center;
  gap: 15px;
  padding: 15px;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.1);
}

.color-indicator {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  border: 2px solid white;
}

.red .color-indicator {
  background-color: #ff4444;
}

.blue .color-indicator {
  background-color: #4444ff;
}

.input-group {
  flex: 1;
  text-align: left;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input {
  width: 100%;
  padding: 8px;
  border: none;
  border-radius: 4px;
  background: rgba(255, 255, 255, 0.9);
  color: #333;
  font-size: 1em;
}

input:focus {
  outline: 2px solid #4a90e2;
}

.start-button {
  padding: 12px 30px;
  font-size: 1.2em;
  background: #4a90e2;
  border: none;
  border-radius: 6px;
  color: white;
  cursor: pointer;
  transition: background 0.3s;
}

.start-button:hover {
  background: #357abd;
}

.start-button:disabled {
  background: #666;
  cursor: not-allowed;
}
</style>