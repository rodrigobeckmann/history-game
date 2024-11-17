<template>
  <div class="game-wrapper">
    <h2 style="font-size: 64px;">É a vez de {{ currentPlayerName }}</h2>
    <div class="game-container">
      <QuestionBox
        :question="currentQuestion"
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

const questionPool = ref([...questions]); // Lista de todas as perguntas disponíveis
const wrongAnswers = ref([]); // Lista de perguntas respondidas incorretamente

function shuffleQuestions() {
  for (let i = questionPool.value.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [questionPool.value[i], questionPool.value[j]] = [questionPool.value[j], questionPool.value[i]];
  }
}

function shuffleOptions(question) {
  const options = [...question.options];
  for (let i = options.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [options[i], options[j]] = [options[j], options[i]];
  }
  return { ...question, options };
}

function loadNextQuestion() {
  // Se a lista principal estiver vazia, adiciona as perguntas erradas de volta
  if (questionPool.value.length === 0) {
    questionPool.value = [...wrongAnswers.value];
    wrongAnswers.value = [];
    shuffleQuestions();
  }
  
  // Pega a próxima pergunta da lista e embaralha suas opções
  const nextQuestion = questionPool.value.shift();
  currentQuestion.value = shuffleOptions(nextQuestion);
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
    // Adiciona a pergunta atual à lista de erradas
    wrongAnswers.value.push(currentQuestion.value);
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
  // Reinicializa as listas de perguntas
  questionPool.value = [...questions];
  wrongAnswers.value = [];
  shuffleQuestions();
  
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

// Inicializa o jogo com as perguntas embaralhadas
watch(() => props.playerNames, () => {
  shuffleQuestions();
  loadNextQuestion();
}, { immediate: true });
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