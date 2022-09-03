<script setup>
  import { ref, onUnmounted, onMounted, computed } from 'vue'
  import { $vfm, VueFinalModal, ModalsContainer } from 'vue-final-modal'
  import Board from './components/Board.vue'
  import Keyboard from './components/Keyboard.vue'
  import { QuestionMarkCircleIcon, CheckIcon, XIcon } from '@heroicons/vue/outline'
  import { originalAcceptedWordList } from './assets/js/originalAcceptedWordList.js'
  import { currentAcceptedWordList} from './assets/js/currentAcceptedWordList.js'
  import { answerWordList } from './assets/js/answerWordList.js'
  import { originalAnswerWordList } from './assets/js/originalAnswerWordList.js'
  import { scrabbleExcludedWordList } from './assets/js/scrabbleExcludedWordList.js'

  let keyboardRows = ref([
    [
      {
        'letter': 'q',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'w',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'e',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'r',
        'state': 1,
        'colored': true
      },
      {
        'letter': 't',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'y',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'u',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'i',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'o',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'p',
        'state': 1,
        'colored': true
      }
    ],
    [
      {
        'letter': 'a',
        'state': 1,
        'colored': true
      },
      {
        'letter': 's',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'd',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'f',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'g',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'h',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'j',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'k',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'l',
        'state': 1,
        'colored': true
      },
      {
        'letter': '_',
        'state': 1,
        'colored': true
      }
    ],
    [
      {
        'letter': 'enter',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'z',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'x',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'c',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'v',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'b',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'n',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'm',
        'state': 1,
        'colored': true
      },
      {
        'letter': 'del',
        'state': 1,
        'colored': true
      }
    ]
  ])

  const wordLength = 5
  let numberOfGuesses = 1
  let showHelpModal = ref(false)
  let guesses = ref(Array())
  let initialGuess = []
  for ( let j=0; j < wordLength; j++ ) {
    initialGuess.push({ 'letter': '', 'state': 0, 'initialized': false, 'colored': false, 'completed': false })
  }
  guesses.value.push(initialGuess)

  let currentPosition = ref(0)
  const onKeyup = (e) => {
    if ( e.ctrlKey || e.altKey || e.metaKey ) {
      return
    }
    onKey(e.key)
  }
  const tileClick = function(e) {
    if ( finished.value ){
      return
    }
    currentPosition.value = e.detail
  }
  window.addEventListener('keyup', onKeyup)
  const tileClickEvent = new Event('tile-click');
  window.addEventListener('tile-click',tileClick)
  onUnmounted(() => {
    window.removeEventListener('keyup', onKeyup)
    window.removeEventListener('tile-click',tileClick)
  })

  const onKey = function(key) {
    if ( showHelpModal.value ) {
      return
    }
    if (/^[a-zA-Z_\-]$/.test(key)) {
      if (key == '-') {
        key = '_'
      }
      fillTile(key.toLowerCase())
    } else if (key === 'Backspace' || key === 'Delete') {
      clearTile()
    } else if (key === 'Enter') {
      return
    } else if ( key == 'ArrowLeft' && currentPosition.value > 0 ) {
      currentPosition.value--
    } else if ( ( key == 'ArrowRight' || key == ' ' ) && currentPosition.value < wordLength - 1 ) {
      currentPosition.value++
    }
  }

  const fillTile = function(key){
    let tile = {}
    if ( currentPosition.value == wordLength ){
      tile = guesses.value[0][currentPosition.value - 1]
    } else {
      tile = guesses.value[0][currentPosition.value]
    }
    tile['letter'] = key
    if ( currentPosition.value < wordLength - 1  ){
      currentPosition.value++
    }
  }

  const clearTile = function() {
    if ( currentPosition.value > 0 && guesses.value[0][currentPosition.value]['letter'] == '' ){
      currentPosition.value--
    }
    let tile = guesses.value[0][currentPosition.value]
    tile['letter'] = ''
  }

  const playerAnswer = computed(() => {
    return guesses.value[0].map((e) => e['letter']).join('')
  })

  const guessComplete = computed(() => {
    return playerAnswer.value.length == wordLength
  })

  const guessOnOriginalAcceptedList = computed(() => {
    if ( playerAnswer.value.length != wordLength || playerAnswer.value.match(/_/) ) {
      return false;
    }

    return originalAcceptedWordList.includes(playerAnswer.value)
  })

  const guessOnCurrentAcceptedList = computed(() => {
    if ( playerAnswer.value.length != wordLength || playerAnswer.value.match(/_/) ) {
      return false;
    }

    return currentAcceptedWordList.includes(playerAnswer.value)
  })

  const guessOnAnswerList = computed(() => {
    if ( playerAnswer.value.length != wordLength || playerAnswer.value.match(/_/) ) {
      return false
    }

    return answerWordList.includes(playerAnswer.value)
  })

  const guessOnOriginalAnswerList = computed(() => {
    if ( playerAnswer.value.length != wordLength || playerAnswer.value.match(/_/) ) {
      return false
    }

    return originalAnswerWordList.includes(playerAnswer.value)
  })

  const guessOnScrabbleList = computed(() => {
    if ( playerAnswer.value.length != wordLength || playerAnswer.value.match(/_/) ) {
      return false
    }

    return (guessOnAnswerList.value || guessOnOriginalAcceptedList.value ) && !scrabbleExcludedWordList.includes(playerAnswer.value)
  })
</script>

<template>
  <div class="container wrap">
    <div class="header row">
      <div class="col-md-3">
      </div>
      <div class="col-md-6">
        <span class="title">Is It A Wordle?</span>
      </div>
      <div class="col-md-3 help">
      </div>
    </div>
    <Board :guesses="guesses" :currentPosition="currentPosition" :wordLength="wordLength"></Board>
    <table id="info">
      <tr>
        <td><CheckIcon class="icon" :class="{'on-list': guessComplete && guessOnAnswerList}"></CheckIcon></td>
        <td><XIcon class="icon" :class="{'not-on-list': guessComplete && !guessOnAnswerList}"></XIcon></td>
        <td>On current Wordle answer list</td>
      </tr>
      <tr>
        <td><CheckIcon class="icon" :class="{'on-list': guessComplete && guessOnOriginalAnswerList}"></CheckIcon></td>
        <td><XIcon class="icon" :class="{'not-on-list': guessComplete && !guessOnOriginalAnswerList}"></XIcon></td>
        <td>On original Wordle answer list,<br/>as used by many variants</td>
      </tr>
      <tr>
        <td><CheckIcon class="icon" :class="{'on-list': guessComplete && guessOnCurrentAcceptedList}"></CheckIcon></td>
        <td><XIcon class="icon" :class="{'not-on-list': guessComplete && !guessOnCurrentAcceptedList}"></XIcon></td>
        <td>On current Wordle accepted list</td>
      </tr>
      <tr>
        <td><CheckIcon class="icon" :class="{'on-list': guessComplete && guessOnOriginalAcceptedList}"></CheckIcon></td>
        <td><XIcon class="icon" :class="{'not-on-list': guessComplete && !guessOnOriginalAcceptedList}"></XIcon></td>
        <td>On original Wordle accepted list,<br/>as used by many variants</td>
      </tr>
      <tr>
        <td><CheckIcon class="icon" :class="{'on-list': guessComplete && guessOnScrabbleList}"></CheckIcon></td>
        <td><XIcon class="icon" :class="{'not-on-list': guessComplete && !guessOnScrabbleList}"></XIcon></td>
        <td>On NA Scrabble list</td>
      </tr>
    </table>
    <Keyboard :rows="keyboardRows"></Keyboard>
    <div class="footer">
      Created by theasylm
    </div>
  </div>
</template>

<style>
  body, html {
    height: 100%;
  }
  body {
    overflow: hidden;
    margin: 0px;
  }
  #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #efefef;
    /**background-color: #08111b;**/
    background-color: #011637;
    height: 100%;
    padding-top: 1em;
  }
  .title {
    font-size: 2em;
  }
  .footer {
    position: absolute;
    bottom: 0.5rem;
    font-size: smaller;
    width: 100%;
    left: 0;
  }
  .warning-message {
    font-size: 1.5rem;
    visibility: hidden;
    margin: 1rem;
  }
  .warning-message.shown {
    visibility: visible;
  }
  .icon {
    width: 2rem;
    margin-top:  -.4rem;
    color: #333;
  }
  .on-list {
    color: green;
  }
  .not-on-list {
    color: red;
  }
  #info {
    margin-left: auto;
    margin-right: auto;
    font-size: 1.5rem;
    text-align: left;
  }
  #info td {
    vertical-align: top;
  }
</style>
