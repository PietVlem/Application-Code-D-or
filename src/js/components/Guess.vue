<template>
  <form class="play-field" :class="{ 'play-field--guess': !editable }">
    <ol class="play-field__color-select">
      <li v-for="(color, buttonIndex) in selectedColors" :key="buttonIndex">
        <template v-if="editable">
          <button class="button button--game" type="button" @click="showOptions = buttonIndex">
            <Color :color="color" />
          </button>

          <fieldset class="color-options" v-if="showOptions === buttonIndex">
            <div v-for="colorOption in possibleColors">
              <input hidden type="radio" name="color" :id="colorOption" :value="colorOption"
                @input="chooseColor(buttonIndex, colorOption)" />
              <label :for="colorOption">
                <Color :color="colorOption" />
              </label>
            </div>
            <div>
              <input hidden type="radio" name="color" id="none" value="none"
                @input="chooseColor(buttonIndex, 'none')" />
              <label for="none">
                <Color color="none">X</Color>
              </label>
            </div>
          </fieldset>

          <!-- <button :id="`button-${buttonIndex}`" @click="showColorOptions(buttonIndex)" type="button"
            class="button button--game" data-bs-toggle="popover" data-bs-placement="bottom" title="Popover title">
            <Color :color="color" />
          </button>

          <div hidden>
            <div :data-name="`popover-content-${buttonIndex}`">
              <fieldset class="color-options">
                {{possibleColors}}
                <div v-for="colorOption in possibleColors">{{ colorOption }}</div>
                <div v-for="colorOption in possibleColors">
                  {{ colorOption }}
                  <input hidden type="radio" :name="`color-button-${buttonIndex}`"
                    :id="`${colorOption}-button-${buttonIndex}`" :value="colorOption"
                    @input="$event => chooseColor(buttonIndex, colorOption)" />
                  <label :for="`${colorOption}-button-${buttonIndex}`">
                    <Color :color="colorOption" />
                  </label>
                </div>
                <div>
                  <input hidden type="radio" :name="`color-button-${buttonIndex}`" :id="`none-button-${buttonIndex}`"
                    value="none" @input="$event => chooseColor(buttonIndex, 'none')" />
                  <label for="`none-button-${buttonIndex}`">
                    <Color color="none">X</Color>
                  </label>
                </div>
              </fieldset>
            </div>
          </div> -->

        </template>
        <Color v-else :color="color" />
      </li>

    </ol>
    <button v-if="editable" class="button button--primary" type="submit" @click.prevent="guess">Guess</button>
    <Pins v-else :pins="pins" />
  </form>
</template>

<script>
import { Popover } from 'bootstrap'
import Color from './Color.vue'
import Pins from './Pins.vue'

export default {
  name: 'Guess',
  components: { Color, Pins },
  props: {
    colors: {
      type: [Array, Boolean],
      default: false
    },
    code: {
      type: Array,
      default: () => [],
    }
  },
  data: () => ({
    possibleColors: [],
    selectedColors: ['none', 'none', 'none', 'none'],
    showOptions: false,
    editable: true
  }),
  mounted() {
    if (this.editable) {
      const popoverTriggerList = [].slice.call(document.querySelectorAll('[data-bs-toggle="popover"]'))
      for (let i = 0; i < popoverTriggerList.length; i++) {
        new Popover(popoverTriggerList[i], {
          html: true,
          content: document.querySelector(`[data-name="popover-content-${i}"]`) || '',
          trigger: 'manual'
        })
      }
    }

    // Get the possible colors from where we set them in the created() function in the App.vue
    this.possibleColors = window.possibleColors
    if (this.colors) {
      // If we pass colors to this component, it means it's a guessed row so it's not editable
      this.editable = false
      this.selectedColors = this.colors
    }
  },
  computed: {
    pins() {
      if (this.editable) {
        // If this row is an editable one, we can't see any pins
        return false
      }

      // Create the logic to show the correct pins based on the guess
      let pins = []
      for (let i = 0; i < this.selectedColors.length; i++) {
        if (this.selectedColors[i] === this.code[i]) {
          pins.unshift('purple')
        } else if (this.code.includes(this.selectedColors[i])) {
          pins.push('black')
        }
      }

      while (pins.length < 4) {
        pins.push('none')
      }

      return pins
    }
  },
  methods: {
    showColorOptions(index) {
      //this.closeAllPopovers()
      console.log(index)
      this.showOptions = index
      //Popover.getInstance(document.getElementById(`button-${index}`)).show()
    },
    chooseColor(index, color) {
      console.log(index, color)
      // If the chosen color already exists in the code, we set the other instance of that color
      // to none, making the newly selected one the only instance of that color in the guess
      const currentIndex = this.selectedColors.findIndex(selected => color === selected)
      console.log('currentIndex', currentIndex)
      if (currentIndex >= 0 && color !== 'none') {
        this.selectedColors[currentIndex] = 'none'
      }

      // Set the selected color and hide the popover
      this.selectedColors[index] = color
      this.showOptions = false
      //Popover.getInstance(document.getElementById(`button-${index}`)).hide()
    },
    guess() {
      //this.closeAllPopovers()
      this.editable = false

      // Determine wether the guess is correct or not
      function arrayEquals(a, b) {
        return Array.isArray(a) &&
          Array.isArray(b) &&
          a.length === b.length &&
          a.every((val, index) => val === b[index]);
      }
      const guessedCorrect = arrayEquals(this.selectedColors, this.code)

      // Emit the guess so it can be handled in the App.vue
      this.$emit('guess', this.selectedColors, guessedCorrect)

      // Set the component back to its starting state
      this.selectedColors = ['none', 'none', 'none', 'none']
      this.editable = true
    },
    closeAllPopovers() {
      document.querySelectorAll('[data-bs-toggle="popover"]').forEach(el => Popover.getInstance(el).hide())
    }
  }
}
</script>