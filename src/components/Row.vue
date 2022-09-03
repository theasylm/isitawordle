<script setup>
  import Tile from './Tile.vue'
  defineProps({
    letters: Array,
    keyboard: Boolean,
    guess: Number,
    currentGuess: Number,
    currentPosition: Number,
    wordLength: Number
  })
</script>

<template>
  <div class="row">
    <Tile v-for="(letter, index) in letters" :letter="letter.letter" :state="letter.state" :keyboard="keyboard" :index="index" :initialized="letter.initialized" :colored="letter.colored" :currentPosition="currentPosition" :guessNumber="guess" :currentGuess="currentGuess" :wordLength="wordLength" v-bind:key="index"></Tile>
  </div>
</template>

<style>
  .row {
    display: flex;
    justify-content: center;
    margin:  .5rem 0;
  }
  .on-answer {
    color: #50834d;
  }
  .not-word {
    color: #842029;
  }
  .not-scrabble {
    color: #ffc107;
  }
</style>