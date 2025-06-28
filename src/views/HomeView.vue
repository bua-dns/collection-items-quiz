<script setup>
import { ref, computed } from 'vue'
import questions from '@/assets/data/questions.json'
import LeafletMap from '@/components/LeafletMap.vue'
import MapView from "@/components/MapView.vue"


// ✅ State
const question = ref(null)
const selectedAnswers = ref(new Set())
const answerIsCorrect = ref(false)
const askedQuestions = ref(new Set())
const questionAlreadyCounted = ref(false)

const totalAttempts = ref(0)
const totalCorrect = ref(0)
const totalWrong = ref(0)
const successRate = computed(() =>
  totalAttempts.value === 0 ? 0 : Math.round((totalCorrect.value / totalAttempts.value) * 100)
)

// ✅ Option generation
function generateOptions(solution, pool) {
  const options = new Set([solution])

  while (options.size < 3) {
    const random = pool[Math.floor(Math.random() * pool.length)]
    options.add(random)
  }
  return Array.from(options).sort(() => Math.random() - 0.5)
}

// ✅ Question generation
function getNewQuestion() {
  // console.log('unfiltered questions', Object.entries(questions))
  const allEntries = Object.entries(questions)
  .filter(([key]) => !askedQuestions.value.has(key))
  // console.log('all entries', allEntries)
  
  if (allEntries.length === 0) return null

  const [key, picked] = allEntries[Math.floor(Math.random() * allEntries.length)]
  // console.log('picked question key', key)
  // console.log('picked question', picked)
  picked.selectedOptions = generateOptions(picked.solution, picked.options)
  // delete picked.options
  picked._key = key
  return picked
}

function nextQuestion() {
  if (question.value?._key) {
    askedQuestions.value.add(question.value._key)
  }

  const next = getNewQuestion()
  if (!next) {
    question.value = null
    return
  }

  question.value = next
  selectedAnswers.value.clear()
  answerIsCorrect.value = false
  questionAlreadyCounted.value = false
}

// ✅ Option selection
function selectOption(option) {
  if (selectedAnswers.value.has(option)) return

  selectedAnswers.value.add(option)

  if (option === question.value.solution) {
    if (!questionAlreadyCounted.value) {
      totalCorrect.value++
      totalAttempts.value++
      questionAlreadyCounted.value = true
    }
    answerIsCorrect.value = true
  } else {
    if (!questionAlreadyCounted.value) {
      totalWrong.value++
      totalAttempts.value++
      questionAlreadyCounted.value = true
    }
  }
}

// ✅ Full reset
function resetAll() {
  askedQuestions.value.clear()
  totalAttempts.value = 0
  totalCorrect.value = 0
  totalWrong.value = 0
  selectedAnswers.value.clear()
  answerIsCorrect.value = false
  questionAlreadyCounted.value = false
  question.value = getNewQuestion()
}

// ✅ Init
question.value = getNewQuestion()

function transformCoords(locationStrg) {
  if (!locationStrg || typeof locationStrg !== 'string') return null

  const match = locationStrg.match(/([+-]?\d+(\.\d+)?),\s*([+-]?\d+(\.\d+)?)/)
  if (!match) return null

  const lat = parseFloat(match[1])
  const long = parseFloat(match[3])

  if (isNaN(lat) || isNaN(long)) return null

  return { lat, long }
}
</script>

<template>
  <div class="content-container">
    <main>
      <h1>Objekte aus den Berliner Universitätssammlungen</h1>

      <div class="quiz" v-if="question">
        <img :src="`/collection-items-quiz/images/${question.image}`" alt="Sammlungsobjekt" />
        <h2>Was ist hier zu sehen?</h2>

        <div class="options">
          <button
            v-for="(opt, index) in question.selectedOptions"
            :key="index"
            @click="selectOption(opt)"
            :class="{
              wrong: selectedAnswers.has(opt) && opt !== question.solution,
              correct: answerIsCorrect && opt === question.solution
            }"
            :disabled="answerIsCorrect || selectedAnswers.has(opt)"
          >
            {{ opt }}
          </button>
        </div>

        <!-- <button v-if="answerIsCorrect" class="next-button" @click="nextQuestion">
          Nächstes Objekt
        </button> -->
        <button
          v-if="answerIsCorrect && totalAttempts < Object.entries(questions).length"
          class="next-button"
          :disabled="!answerIsCorrect"
          @click="nextQuestion"
        >
          Nächstes Objekt
        </button>
      </div>

      <div v-if="!question" class="quiz">
        <p>✅ Du hast alle Fragen durchgespielt!</p>
        <button class="reset-button" @click="resetAll()">
          Quiz zurücksetzen
        </button>
      </div>
    </main>

    <aside>
      <h2>Dein Ergebnis</h2>
      <p><strong>{{ totalAttempts }} / {{ Object.entries(questions).length }}</strong> Aufgaben</p>
      <p><strong>{{ totalCorrect }}</strong> richtig</p>
      <p><strong>{{ totalWrong }}</strong> falsch</p>
      <p><strong>{{ successRate }}%</strong> korrekt</p>
      <button class="reset-button" @click="resetAll()">
        Quiz zurücksetzen
      </button>
      <div class="item-info" v-if="answerIsCorrect">
        <h3>{{ question.solution }}</h3>
        <div>
          Ein Objekt aus der Sammlung:<br />
          {{ question.collection }}
        </div>
      </div>

    </aside>
  </div>
</template>

<style scoped lang="scss">
@import 'leaflet/dist/leaflet.css';

.content-container {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  padding: 1rem;
  min-height: 90vh;
  background-color: #fff;
  background-image: url('@/assets/images/quiz.svg');
  background-repeat: no-repeat;
  background-position: left 3rem top 7rem;
  background-size: 6rem auto; /* Adjust size as needed */
  border-radius: 6px;
  box-shadow: 2px 4px 6px rgba(0, 0, 0, .4);

  @media (min-width: 768px) {
    flex-direction: row;
    padding: 2rem;
  }
}

main {
  flex: 2;
  text-align: center;
  h1 {
    font-size: 1.125rem;
    margin-bottom: 1.5rem;

    @media (min-width: 768px) {
      font-size: 2rem;
    }
  }

  img {
    max-width: 28.8rem; // 20% larger than 24rem
    height: auto;
    margin-bottom: 1.5rem;
    border-radius: 0.5rem;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .quiz {
    display: flex;
    flex-direction: column;
    align-items: center;

    h2 {
      margin: 1.5rem 0 1rem;
      font-size: 1.125rem;

      @media (min-width: 768px) {
        font-size: 1.5rem;
      }
    }
  }

  .options {
    display: flex;
    flex-wrap: wrap;
    gap: 0.75rem;
    margin-top: 1.25rem;

    button {
      padding: 0.4rem 0.8rem;
      font-size: 0.8rem;
      border: none;
      border-radius: 0.5rem;
      background-color: #eee;
      cursor: pointer;
      transition: background-color 0.3s;

      &:hover {
        background-color: #ddd;
      }

      &.correct {
        background-color: #4caf50;
        color: white;
      }

      &.wrong {
        background-color: #f44336;
        color: white;
      }

      &:disabled {
        opacity: 0.9;
        cursor: default;
      }

      @media (min-width: 768px) {
        padding: 0.6rem 1rem;
        font-size: 0.9rem;
      }
    }
  }

  .next-button {
    margin-top: 1.25rem;
    padding: 0.6rem 1.2rem;
    font-size: 1rem;
    background-color: #1976d2;
    color: white;
    border: none;
    border-radius: 0.5rem;
    cursor: pointer;

    &:hover {
      background-color: #1565c0;
    }
  }
}

aside {
  flex: 0.5; // reduced from 1 to 0.5
  padding-top: 1rem;
  border-top: 1px solid #ddd;
  color: #444;

  @media (min-width: 768px) {
    border-top: none;
    border-left: 1px solid #ddd;
    padding-left: 2rem;
    padding-top: 0;
  }

  h2 {
    font-size: 1.1rem;
    margin-bottom: 1rem;

    @media (min-width: 768px) {
      font-size: 1.25rem;
    }
  }
  .item-info {
    margin-top: 1rem;
    font-size: 0.9rem;
    color: #666;

    h3 {
      font-weight: bold;
      margin-bottom: 0.5rem;
    }

    div {
      margin-top: 0.5rem;
    }
  }
}
.reset-button {
    margin-top: 1rem;
    padding: 0.5rem 1rem;
    background-color: #9e9e9e;
    color: white;
    border: none;
    border-radius: 0.5rem;
    cursor: pointer;

    &:hover {
      background-color: #757575;
    }

    @media (min-width: 768px) {
      font-size: 1rem;
    }
  }
  .map-box {
  width: 36rem;
  height: 24remx;
  border-radius: 8px;
  overflow: hidden;

  // optional: box shadow for visual hierarchy
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}
</style>

