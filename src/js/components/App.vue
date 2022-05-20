<template>
  <div class="site">

    <!-- Hero -->
    <header>
      <div class="container">
        <div class="row">
          <div class="col-sm hero">
            <h1>Code d'Or Mastermind</h1>
            <button @click="startGame" type="button" class="button button--primary">
              new game
            </button>
          </div>
        </div>
      </div>
    </header>

    <!-- game -->
    <div class="body container">
      <div class="body__inner-wrapper row">
        <div class="col-sm">
          <div class="solution">
            <ol v-if="code.length > 0">
              <li v-for="(color, index) in code" :key="index">
                <Color :color="gameOver ? color : 'none'" />
              </li>
            </ol>
            <p id="countdown">5:00</p>
          </div>
          <Guess v-if="!gameOver" :code="code" @guess="handleGuess" />
          <div v-for="(guess, index) in guesses" :key="index">
            <Guess :code="code" :colors="guess" />
          </div>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <footer>
      <div class="container">
        <div class="row">
          <a href="https://codedor.be" target="_blank" class="col-sm">&copy; Code d'Or</a>
        </div>
      </div>
    </footer>

    <!-- Modal -->
    <div class="modal fade" ref="myModal" tabindex="-1" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h2 id="myModalLabel">{{ modal.title }}</h2>
            <button type="button" class="btn-close" @click="modal.hide()" aria-label="Close"></button>
          </div>
          <div class="modal-footer">
            <button type="button" class="button button--secondary" @click="modal.hide()">Close</button>
            <button type="button" class="button button--primary" @click="playAgain">Play again</button>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import { Modal } from 'bootstrap'
import Color from './Color.vue'
import Guess from './Guess.vue'

export default {
  name: 'App',
  components: { Color, Guess },
  data: () => ({
    possibleColors: ['red', 'orange', 'yellow', 'green', 'blue', 'purple'],
    code: [],
    gameOver: false,
    guesses: [],
    modal: null,
    timer: 0,
    modal: {
      title: '',
    }
  }),
  created() {
    window.possibleColors = this.possibleColors
  },
  mounted() {
    this.startGame()

    this.modal = new Modal(this.$refs.myModal)
  },
  methods: {
    startGame() {
      // Reset values from previous game
      this.stopTimer()
      this.gameOver = false
      this.guesses = []

      // Set values for new game
      this.setRandomCode()

      // timer
      this.resetTimer()
      this.runTimer()
    },
    resetTimer() {
      this.time = 300
    },
    runTimer() {
      this.myInterval = setInterval(this.updateCountdown, 1000)
    },
    updateCountdown() {
      let minutes = Math.floor(this.time / 60)
      let seconds = this.time % 60
      seconds = seconds < 10 ? '0' + seconds : seconds

      document.querySelector('#countdown').innerHTML = `${minutes}:${seconds}`

      this.time--

      if (this.time < 0) {
        this.gameOver = true
        this.modal.title = "You've lost!"
        this.modal.show()
        this.stopTimer()
      }
    },
    stopTimer() {
      clearInterval(this.myInterval)
    },
    setRandomCode() {
      this.code = []
      // Our code is 4 colors long, so we loop to add a color until that is the case
      while (this.code.length < 4) {
        this.code.push(this.addColorToCode())
      }
    },
    addColorToCode() {
      // A color can only be used once in the code so we start by filtering out all the colors
      // that have been used already and then select a random one from the remaining colors
      const remainingColors = this.possibleColors.filter(color => !this.code.includes(color))
      const randomIndex = Math.floor(Math.random() * remainingColors.length)
      return remainingColors[randomIndex]
    },
    handleGuess(guess, guessCorrect) {
      if (guessCorrect) {
        // If the guess was correct, the code at the top gets revealed and no more guesses can be made
        // Show a modal with a button to start a new game
        this.gameOver = true
        this.modal.title = "You've won!"
        this.modal.show()
        this.stopTimer()
      }

      // Add the guess to the array of past guesses
      this.guesses.push(guess)
    },

    playAgain() {
      this.modal.hide()
      this.startGame()
    }

  }
}
</script>
