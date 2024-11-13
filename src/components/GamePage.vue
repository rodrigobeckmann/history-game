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
  </div>
</template>

<script setup>
import { ref, computed, watch, onUnmounted } from 'vue';
import GameTable from './GameTable.vue';
import QuestionBox from './QuestionBox.vue';
import FeedbackModal from './FeedbackModal.vue';
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
    showFeedback("Resposta Correta!", true);
  } else {
    wrongSound.play();
    showFeedback("Resposta Incorreta!", false);
  }
  switchTurn();
}

function showFeedback(message, isSuccess = true) {
  if (feedbackTimeout.value) clearTimeout(feedbackTimeout.value);
  if (turnChangeTimeout.value) clearTimeout(turnChangeTimeout.value);

  feedbackMessage.value = message;
  feedbackType.value = isSuccess ? 'success' : 'error';
  isFeedbackVisible.value = true;
  
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
}

function switchTurn() {
  currentPlayerIndex.value = (currentPlayerIndex.value + 1) % 2;
  loadNextQuestion();
}

onUnmounted(() => {
  if (feedbackTimeout.value) clearTimeout(feedbackTimeout.value);
  if (turnChangeTimeout.value) clearTimeout(turnChangeTimeout.value);
});

correctSound.volume = 0.3;
wrongSound.volume = 0.1;
</script>

<style>
.game-wrapper {
  text-align: center;
}

.game-container {
  display: flex;
  align-items: center;
  gap: 50px;
  margin-top: 20px;
}

.game-table {
  flex: 2;
}

.question-box {
  flex: 1;
  background-color: #333;
  color: #fff;
  padding: 20px;
  border-radius: 8px;
  text-align: center;
}

button {
  margin: 5px;
}

.feedback-modal.turn {
  background-color: #4a90e2;
  font-size: 1.5em;
  padding: 20px 40px;
  transform: scale(1.2);
  transition: all 0.3s ease;
}
</style>