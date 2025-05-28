<template>
  <div class="flex flex-col items-center gap-4">
    <div class="grid grid-cols-4 sm:grid-cols-4 gap-4">
      <Card
          v-for="card in cards"
          :key="card.id"
          :card="card"
          :onClick="() => flipCard(card)"
      />
    </div>

    <div class="text-center mt-5 space-y-2">
      <div class="mb-10 flex gap-5">
        <p>🎯 Movimentos: <strong>{{ moves }}</strong></p>
        <p>⏱️ Tempo: <strong>{{ elapsedTime }}s</strong></p>
      </div>

      <p v-if="isWin" class="text-green-600 font-bold text-xl animate-pulse">
        🎉 Parabéns! Você venceu!
      </p>

      <div v-if="isWin" class="mt-10">
        <div class="flex mt-10 mb-10">
          <button @click="resetGame" class="btn me-5 flex items-center gap-2">
            <ArrowPathIcon class="w-5 h-5" />
            Jogar Novamente
          </button>

          <button @click="$emit('backToMenu')" class="btn bg-gray-500 hover:bg-gray-600 flex items-center gap-2">
            <HomeIcon class="w-5 h-5" />
            Menu
          </button>
        </div>

        <h3 class="mt-4 font-semibold mb-5">🏆 Seus Recordes:</h3>
        <ul class="text-sm">
          <li v-for="(r, i) in highScores" :key="i">
            {{ i + 1 }}º - {{ r.moves }} movimentos, {{ r.time }}s ({{ r.date }})
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount, watch } from 'vue'
import Card from './Card.vue'
import { themes } from '../assets/themes'
import { ArrowPathIcon, HomeIcon } from '@heroicons/vue/24/outline'

const props = defineProps<{
  theme: keyof typeof themes,
  level: 'easy' | 'medium' | 'hard'
}>()

const emit = defineEmits(['backToMenu'])

type CardType = {
  id: number
  symbol: string
  flipped: boolean
  matched: boolean
}

const cards = ref<CardType[]>([])
const flipped = ref<CardType[]>([])
const moves = ref(0)
const startTime = ref<number | null>(null)
const elapsedTime = ref(0)
let timer: any = null

const highScores = ref<{ moves: number, time: number, date: string }[]>([])

const getPairCount = () => {
  return props.level === 'easy' ? 4 : props.level === 'medium' ? 6 : 8
}

const createDeck = (): CardType[] => {
  const base = [...themes[props.theme]]
  const count = getPairCount()
  const selected = base.slice(0, count)
  const pairSymbols = [...selected, ...selected]
  return pairSymbols
      .map((symbol, i) => ({
        id: i + Math.random(),
        symbol,
        flipped: false,
        matched: false
      }))
      .sort(() => Math.random() - 0.5)
}

const resetGame = () => {
  cards.value = createDeck()
  flipped.value = []
  moves.value = 0
  elapsedTime.value = 0
  startTime.value = Date.now()
  if (timer) clearInterval(timer)
  timer = setInterval(() => {
    if (startTime.value) {
      elapsedTime.value = Math.floor((Date.now() - startTime.value) / 1000)
    }
  }, 1000)
}

const playSound = (file: string) => {
  const audio = new Audio(`/src/assets/${file}`)
  audio.play()
}

const flipCard = (card: CardType) => {
  if (card.flipped || card.matched || flipped.value.length === 2) return
  card.flipped = true
  flipped.value.push(card)
  playSound('flip.wav')

  if (flipped.value.length === 2) {
    moves.value++
    const [a, b] = flipped.value
    if (a.symbol === b.symbol) {
      a.matched = b.matched = true
      flipped.value = []
      playSound('match.wav')
    } else {
      setTimeout(() => {
        a.flipped = b.flipped = false
        flipped.value = []
      }, 1000)
    }
  }
}

const isWin = computed(() => cards.value.length && cards.value.every(c => c.matched))

const saveRecord = () => {
  const key = `memory-records-${props.theme}-${props.level}`
  const existing = JSON.parse(localStorage.getItem(key) || '[]')
  const newRecord = {
    moves: moves.value,
    time: elapsedTime.value,
    date: new Date().toLocaleDateString()
  }
  const updated = [...existing, newRecord]
      .sort((a, b) => a.moves - b.moves || a.time - b.time)
      .slice(0, 5)
  localStorage.setItem(key, JSON.stringify(updated))
  highScores.value = updated
}

watch(isWin, (win) => {
  if (win) {
    clearInterval(timer)
    playSound('win.wav')
    saveRecord()
  }
})

onMounted(() => {
  resetGame()
  const key = `memory-records-${props.theme}-${props.level}`
  const saved = localStorage.getItem(key)
  if (saved) {
    highScores.value = JSON.parse(saved)
  }
})

onBeforeUnmount(() => clearInterval(timer))
</script>

<style scoped>
.btn {
  @apply mt-2 px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded transition;
}
</style>
