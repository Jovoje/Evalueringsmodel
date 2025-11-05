// ...existing code...
<script setup>
import { ref, computed, watch } from 'vue'
import './style/global.css' 


const modes = [
  { key: 'begynder', label: 'Begynder' },
  { key: 'ekspertsystem', label: 'Ekspertsystem' },
  { key: 'mellemting', label: 'Mellemting' }
]

const questionsByMode = {
  begynder: [
    'Let at lære',
    'Kontrol',
    'Fejlhåndtering',
    'Hastighed',
    'Look & feel'
  ],
  ekspertsystem: [
    'Fleksibilitet',
    'Avanceret kontrol',
    'Fejltilstande håndtering',
    'Ydeevne',
    'Udvidet brugerfeedback'
  ],
  mellemting: [
    'Balance i funktioner',
    'Forudsigelig kontrol',
    'Fejlbeskeder',
    'Responsivitet',
    'Design & konsistens'
  ]
}

const selectedMode = ref(modes[0].key)
const questions = ref(questionsByMode[selectedMode.value])
const answers = ref(Array(questions.value.length).fill(null))

watch(selectedMode, (newMode) => {
  questions.value = questionsByMode[newMode] || []
  answers.value = Array(questions.value.length).fill(null)
})

function setAnswer(idx, val) {
  const n = Number(val)
  answers.value[idx] = Number.isFinite(n) && n >= 1 && n <= 5 ? n : null
}

const average = computed(() => {
  // only show result when ALL inputs have a valid number (1-5)
  if (!answers.value.length) return null
  if (answers.value.some(v => v === null || v === undefined || Number.isNaN(Number(v)))) {
    return null
  }
  const nums = answers.value.map(v => Number(v))
  const sum = nums.reduce((s, v) => s + v, 0)
  return Math.round((sum / nums.length) * 10) / 10 // one decimal
})
</script>

<template>
  <div class="app">
    <h1>Evalueringsmodel <br>- scoring af brugertilfredshed</h1>
    <header class="mode-select">
      <button
        v-for="m in modes"
        :key="m.key"
        :class="{ active: selectedMode === m.key }"
        @click="selectedMode = m.key"
      >
        {{ m.label }}
      </button>
    </header>

    <main class="scheme">
      <div class="questions">
        <div
          v-for="(q, i) in questions"
          :key="i"
          class="row"
        >
          <div class="label">{{ q }}</div>
          <div class="input">
            <select v-model="answers[i]" @change="setAnswer(i, answers[i])">
              <option :value="null"></option>
              <option v-for="n in 5" :key="n" :value="n">{{ n }}</option>
            </select>
          </div>
        </div>
      </div>
    </main>

    <footer class="result">
      Score:
      <span class="avg">
        {{ average === null ? '–' : average }}
      </span>
    </footer>
  </div>
</template>

<style scoped>

h1 {
  font-size: 24px;
  margin-bottom: 16px;
  margin-top: 6px;
  font-weight: 700;
  color: var(--accent);
  line-height: 1.2;
  font-weight: 800;
  font-family: 'Manrope-Semibold', sans-serif;
}

:root {
  --accent: #1500d4;
  --card-bg: rgba(255,255,255,0.6);
  --dashed: rgba(0,0,0,0.28);
  --page-beige: #ebe8e4;
}

.app {
  font-family: 'Manrope-light', sans-serif;
  max-width: 720px;
  margin: 20px auto;
  /* border: 1px solid #ddd; */
  padding: 16px;
  border-radius: 6px;
  background: #ebe8e4;
  box-shadow: 0 2px 6px rgba(0,0,0,0.06);

}

.mode-select {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;
}

.mode-select button {
  padding: 6px 12px;
  border-radius: 6px;
  font-family: 'Manrope', sans-serif;
  border: 0;
  background: rgba(255,255,255,0.9);
  color: var(--accent);
  font-weight: 600;
  box-shadow: 0 2px 6px rgba(0,0,0,0.06);
  cursor: pointer;
}

.mode-select button.active {
  background: #1500d4;
  color: white;
  border-color: #1500d4;
}

.scheme .questions {
  display: grid;
  gap: 8px;
}

.row {
  display: grid;
  grid-template-columns: 1fr 100px;
  align-items: center;
  gap: 12px;
  padding: 8px;
  border-radius: 8px;
  background: rgba(255,255,255,0.6);
  border: 3px dashed var(--dashed);
  box-shadow: 0 6px 18px rgba(10,10,10,0.03);
}

.label {
  font-size: 16px;
}

.input select {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  width: 100%;
  padding: 8px 12px;
  border-radius: 6px;
  border: 2px dashed rgba(0,0,0,0.14);
  background: transparent;
  font-size: 15px;
  color: #1500d4;
  text-align: center;
  cursor: pointer;
  box-shadow: none;
}

.input select:focus {
  outline: none;
  border-style: solid;
  border-color: var(--accent);
  box-shadow: 0 6px 18px rgba(21,0,212,0.08);
}

.result {
  margin-top: 22px;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  gap: 12px;
}

.avg {
  min-width: 64px;
  text-align: center;
  padding: 8px 12px;
  border-radius: 6px;
  border: 3px dashed var(--dashed);
  background: rgba(255,255,255,0.85);
  font-weight: 700;
}
</style>
// ...existing code...