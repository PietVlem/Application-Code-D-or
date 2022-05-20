<template>
  <div>
    <h1>Mastermind</h1>
    <button @click="startGame" type="button">
      new game
    </button>
    <ol v-if="code.length > 0">
      <li v-for="(color, index) in code" :key="index">
        <Color :color="guessed ? color : 'none'" />
      </li>
    </ol>
    <div v-if="!guessed">
      <Guess @guess="handleGuess" />
    </div>
    <div>
      <div v-for="(guess, index) in guesses" :key="index">
        <Guess :colors="guess" />
      </div>
    </div>
  </div>
</template>

<script>
import Color from './Color.vue'
import Guess from './Guess.vue'

export default {
  name: 'App',
  components: { Color, Guess },
  data: () => ({
    possibleColors: ['red', 'orange', 'yellow', 'green', 'blue', 'purple'],
    code: [],
    guessed: false,
    guesses: []
  }),
  created () {
    window.possibleColors = this.possibleColors
  },
  mounted () {
    this.startGame()
  },
  methods: {
    startGame () {
      // Reset values from previous game
      this.guessed = false
      this.guesses = []

      // Set values for new game
      this.setRandomCode()
    },
    setRandomCode () {
      this.code = []
      // Our code is 4 colors long, so we loop to add a color until that is the case
      while (this.code.length < 4) {
        this.code.push(this.addColorToCode())
      }
    },
    addColorToCode () {
      // A color can only be used once in the code so we start by filtering out all the colors
      // that have been used already and then select a random one from the remaining colors
      const remainingColors = this.possibleColors.filter(color => !this.code.includes(color))
      const randomIndex =  Math.floor(Math.random() * remainingColors.length)
      return remainingColors[randomIndex]
    },
    handleGuess (guess, guessCorrect) {
      if (guessCorrect) {
        // If the guess was correct, the code at the top gets revealed and no more guesses can be made
        // TODO - show a modal with a button to start a new game
        this.guessed = true
      }

      // Add the guess to the array of past guesses
      this.guesses.push(guess)
    }
  }
}
</script>
