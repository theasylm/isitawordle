<script setup>
  import { ref } from 'vue'
  import Row from './Row.vue'
  const props = defineProps({
    guesses: Array,
    currentPosition: Number,
    wordLength: Number
  })
</script>

<template>
  <div class="board" id="board">
    <Row v-for="guess,i in guesses" :letters="guess" :guess="i" :currentGuess="i" :currentPosition="currentPosition" :wordLength="wordLength" v-bind:key="i"></Row>
  </div>
</template>

<style>
  .board {
    height: 5rem;
    overflow: scroll;
    -ms-overflow-style: none;  /* IE and Edge */
    scrollbar-width: none;  /* Firefox */
  }
  .board::-webkit-scrollbar {
    display: none;
  }
  .board.has-title {
    height: calc(100vh - 24.5rem);
  }
</style>