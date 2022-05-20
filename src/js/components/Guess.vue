<template>
  <form class="play-field" :class="{ 'play-field--guess': !editable }">
    <ol class="play-field__color-select">
      <li v-for="(color, index) in selectedColors" :key="index">
        <template v-if="editable">
          <button class="button button--game" type="button" @click="$event => showOptions = index">
            <Color :color="color" />
          </button>

          <fieldset class="color-options" v-if="showOptions === index">
            <div v-for="colorOption in possibleColors">
              <input hidden type="radio" name="color" :id="colorOption" :value="colorOption"
                @input="$event => chooseColor(index, colorOption)" />
              <label :for="colorOption">
                <Color :color="colorOption" />
              </label>
            </div>
            <div>
              <input hidden type="radio" name="color" id="none" value="none"
                @input="$event => chooseColor(index, 'none')" />
              <label for="none">
                <Color color="none">X</Color>
              </label>
            </div>
          </fieldset>

          <!-- <button 
              data-bs-toggle="popover" 
              data-bs-placement="bottom" 
              class="button button--game" 
              type="button" 
              @click="$event => showOptions = index">
              {{ index }}
            <Color :color="color" />
          </button> -->

          <!-- popover -->
          <!-- <div style="display: none;" > 
            <div data-name="popover-content">
                <fieldset class="popover-content__inner-wrapper" v-if="showOptions === index">
                  <div class="radio-color" v-for="colorOption in possibleColors">
                    <input hidden type="radio" name="color" :id="colorOption" :value="colorOption" @input="$event => chooseColor(index, colorOption)" />
                    <label :for="colorOption">
                      <Color :color="colorOption" />
                    </label>
                  </div>
                  <div>
                    <input hidden type="radio" name="color" id="none" value="none" @input="$event => chooseColor(index, 'none')" />
                    <label for="none">
                      <Color color="none">
                        <span>X</span>
                      </Color>
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
import { Popover } from "bootstrap";
import Color from './Color.vue'
import Pins from './Pins.vue'

export default {
  name: 'Guess',
  components: { Color, Pins },
  props: {
    colors: {
      type: [Array, Boolean],
      default: false
    }
  },
  data: () => ({
    possibleColors: [],
    selectedColors: ['none', 'none', 'none', 'none'],
    showOptions: false,
    editable: true
  }),
  mounted() {
    // bootstrap popover
    const popoverTriggerList = document.querySelectorAll('[data-bs-toggle="popover"]')
    if (popoverTriggerList) {
      const options = {
        html: true,
        content: document.querySelector('[data-name="popover-content"]'),
        //trigger: 'focus'
      }
      const popoverList = [...popoverTriggerList].map(popoverTriggerEl => new Popover(popoverTriggerEl, options))
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

      const pins = []

      // TODO - create the logic to show the correct pins based on the guess
      // a correct color in the correct spot = purple pin
      // a correct color in the wrong spot   = black pin
      // a wrong color                       = grey/"none" pin
      // pins need to be shown in the order purple - black - grey

      return pins
    }
  },
  methods: {
    chooseColor(index, color) {
      // If the chosen color already exists in the code, we set the other instance of that color
      // to none, making the newly selected one the only instance of that color in the guess
      const currentIndex = this.selectedColors.findIndex(selected => color === selected)
      if (currentIndex >= 0 && color !== 'none') {
        this.selectedColors[currentIndex] = 'none'
      }

      // Set the selected color and hide the popover
      this.selectedColors[index] = color
      this.showOptions = false
    },
    guess() {
      this.editable = false

      // TODO - determine wether the guess is correct or not
      const guessedCorrect = false

      // Emit the guess so it can be handled in the App.vue
      this.$emit('guess', this.selectedColors, guessedCorrect)

      // Set the component back to its starting state
      this.selectedColors = ['none', 'none', 'none', 'none']
      this.editable = true
    }
  }
}
</script>