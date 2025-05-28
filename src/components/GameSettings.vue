<template>
  <div class="flex flex-col gap-6 text-center mb-6 max-w-xs mx-auto">
    <div class="flex flex-col items-start">
      <label class="font-semibold mb-1 text-gray-700">Tema:</label>
      <div class="relative w-full">
        <select
            v-model="selectedTheme"
            class="appearance-none w-full bg-white border border-gray-300 rounded-md py-2 px-3 pr-10 shadow-sm
               focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-green-500 transition"
        >
          <option v-for="(v, key) in themes" :key="key" :value="key">{{ key }}</option>
        </select>
        <!-- Ícone de seta para baixo -->
        <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-3">
          <svg class="h-5 w-5 text-gray-400" xmlns="http://www.w3.org/2000/svg" fill="none"
               viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
          </svg>
        </div>
      </div>
    </div>

    <div class="flex flex-col items-start">
      <label class="font-semibold mb-1 text-gray-700">Dificuldade:</label>
      <div class="relative w-full">
        <select
            v-model="selectedLevel"
            class="appearance-none w-full bg-white border border-gray-300 rounded-md py-2 px-3 pr-10 shadow-sm
               focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-green-500 transition"
        >
          <option value="easy">Fácil (4 pares)</option>
          <option value="medium">Médio (6 pares)</option>
          <option value="hard">Difícil (8 pares)</option>
        </select>
        <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-3">
          <svg class="h-5 w-5 text-gray-400" xmlns="http://www.w3.org/2000/svg" fill="none"
               viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
          </svg>
        </div>
      </div>
    </div>

    <button
        @click="$emit('start', { theme: selectedTheme, level: selectedLevel })"
        class="px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700 transition w-full max-w-xs"
    >
      Começar Jogo
    </button>
  </div>

</template>

<script setup lang="ts">
import { ref } from 'vue'
import { themes } from '../assets/themes'

const selectedTheme = ref('Animais')
const selectedLevel = ref('easy')

defineEmits(['start'])
</script>
