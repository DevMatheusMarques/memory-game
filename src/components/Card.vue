<template>
  <div
      @click="onClick"
      class="w-20 h-28 sm:w-24 sm:h-32 flex items-center justify-center text-4xl font-bold rounded-2xl shadow-md cursor-pointer transform transition-transform duration-300"
      :class="card.flipped || card.matched
      ? 'bg-white/90 text-black scale-105'
      : 'bg-blue-500/80 text-transparent hover:scale-105'"
  >
    <span>{{ card.symbol }}</span>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  card: {
    id: number
    symbol: string
    flipped: boolean
    matched: boolean
  }
  onClick: () => void
}>()
</script>