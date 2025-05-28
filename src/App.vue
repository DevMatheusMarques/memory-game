<template>
  <div class="h-screen overflow-auto flex flex-col justify-center items-center p-4 text-center gap-5">
  <h1 class="text-3xl font-bold mb-4 text-blue-800">🧠 Jogo da Memória</h1>
    <GameSettings v-if="!gameStarted" @start="startGame" />
    <MemoryBoard
        v-else
        :theme="theme"
        :level="level"
        @backToMenu="reset"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import GameSettings from './components/GameSettings.vue'
import MemoryBoard from './components/MemoryBoard.vue'

const gameStarted = ref(false)
const theme = ref('animals')
const level = ref('easy')

const startGame = ({ theme: t, level: l }) => {
  theme.value = t
  level.value = l
  gameStarted.value = true
}

const reset = () => {
  gameStarted.value = false
}
</script>
