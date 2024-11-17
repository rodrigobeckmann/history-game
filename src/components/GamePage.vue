<template>
  <div class="game-wrapper">
    <h2 style="font-size: 64px;">É a vez de {{ currentPlayerName }}</h2>
    <div class="game-container">
      <QuestionBox
        :question="currentQuestion"
        :playerColor="currentPlayerColor"
        :isVisible="isGameStarted"
        @answer="handleAnswer"
        class="question-box"
      />
      <GameTable ref="gameTable" class="game-table" />
    </div>
    <FeedbackModal :message="feedbackMessage" :isVisible="isFeedbackVisible" :type="feedbackType" />
    <JustificationModal 
      :isVisible="isJustificationVisible"
      :justification="currentQuestion?.justification"
      @close="closeJustification"
    />
    <VictoryModal 
      :isVisible="isVictoryModalVisible"
      :playerName="currentPlayerName"
      @restart="restartGame"
    />
  </div>
</template>

<script setup>
import { ref, computed, watch, onUnmounted } from 'vue';
import GameTable from './GameTable.vue';
import QuestionBox from './QuestionBox.vue';
import FeedbackModal from './FeedbackModal.vue';
import JustificationModal from './JustificationModal.vue';
import VictoryModal from './VictoryModal.vue';
import questions from '../data/historia-brasil.json';

const props = defineProps(['playerNames']);

const gameTable = ref(null);
const isGameStarted = ref(true);
const currentQuestion = ref(null);
const currentPlayerIndex = ref(0);

const feedbackMessage = ref("");
const isFeedbackVisible = ref(false);
const feedbackType = ref('');
const isChangingTurn = ref(false);
const isJustificationVisible = ref(false);
const isVictoryModalVisible = ref(false);

const feedbackTimeout = ref(null);
const turnChangeTimeout = ref(null);

const correctSound = new Audio(new URL('../assets/sounds/correct.mp3', import.meta.url).href);
const wrongSound = new Audio(new URL('../assets/sounds/wrong.mp3', import.meta.url).href);

const currentPlayerName = computed(() => props.playerNames[currentPlayerIndex.value]);
const currentPlayerColor = computed(() => (currentPlayerIndex.value === 0 ? 'Red' : 'Blue'));

watch(() => props.playerNames, () => loadNextQuestion(), { immediate: true });

function loadNextQuestion() {
  currentQuestion.value = questions[currentPlayerIndex.value % questions.length];
}

function handleAnswer(answer) {
  const correctAnswer = currentQuestion.value.answer;
  if (answer.trim().toLowerCase() === correctAnswer.toLowerCase()) {
    gameTable.value.movePawn(currentPlayerIndex.value);
    correctSound.play();
    
    if (checkVictory(currentPlayerIndex.value)) {
      isVictoryModalVisible.value = true;
      return;
    }
    
    showFeedback("Resposta Correta!", true);
    switchTurn();
  } else {
    wrongSound.play();
    showFeedback("Resposta Incorreta!", false);
    isJustificationVisible.value = true;
  }
}

function showFeedback(message, isSuccess = true) {
  if (feedbackTimeout.value) clearTimeout(feedbackTimeout.value);
  if (turnChangeTimeout.value) clearTimeout(turnChangeTimeout.value);

  feedbackMessage.value = message;
  feedbackType.value = isSuccess ? 'success' : 'error';
  isFeedbackVisible.value = true;
  
  if (isSuccess) {
    feedbackTimeout.value = setTimeout(() => {
      isFeedbackVisible.value = false;
      isChangingTurn.value = true;
      feedbackMessage.value = `Vez de ${props.playerNames[(currentPlayerIndex.value + 1) % 2]}!`;
      feedbackType.value = 'turn';
      isFeedbackVisible.value = true;
      
      turnChangeTimeout.value = setTimeout(() => {
        isFeedbackVisible.value = false;
        isChangingTurn.value = false;
      }, 1500);
    }, 1000);
  } else {
    feedbackTimeout.value = setTimeout(() => {
      isFeedbackVisible.value = false;
    }, 1000);
  }
}

function switchTurn() {
  currentPlayerIndex.value = (currentPlayerIndex.value + 1) % 2;
  loadNextQuestion();
}

function closeJustification() {
  isJustificationVisible.value = false;
  feedbackMessage.value = `Vez de ${props.playerNames[(currentPlayerIndex.value + 1) % 2]}!`;
  feedbackType.value = 'turn';
  isFeedbackVisible.value = true;
  
  turnChangeTimeout.value = setTimeout(() => {
    isFeedbackVisible.value = false;
    isChangingTurn.value = false;
  }, 1500);
  
  switchTurn();
}

function restartGame() {
  isGameStarted.value = true;
  isJustificationVisible.value = false;
  isVictoryModalVisible.value = false;
  currentPlayerIndex.value = 0;
  gameTable.value.resetPawns();
  loadNextQuestion();
}

function checkVictory(playerIndex) {
  const pawnPosition = gameTable.value.getPawnPosition(playerIndex);
  return pawnPosition.row === 3 && pawnPosition.col === 0;
}

onUnmounted(() => {
  if (feedbackTimeout.value) clearTimeout(feedbackTimeout.value);
  if (turnChangeTimeout.value) clearTimeout(turnChangeTimeout.value);
});

correctSound.volume = 0.3;
wrongSound.volume = 0.1;
</script>

<style scoped>
.game-wrapper {
  text-align: center;
  padding: 20px;
}

.game-container {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  gap: 50px;
  margin-top: 20px;
  max-width: 1400px;
  margin: 20px auto;
}

.game-table {
  flex: 1;
}

.question-box {
  flex: 0 0 400px;
}

h2 {
  color: white;
  margin-bottom: 30px;
}

.feedback-modal.turn {
  background-color: #4a90e2;
  font-size: 1.5em;
  padding: 20px 40px;
  transform: scale(1.2);
  transition: all 0.3s ease;
}
</style>