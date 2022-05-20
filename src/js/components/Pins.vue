<template>
  <ul class="pins">
    <li v-for="pin in pins">
      <Color :pin="true" :color="pin" />
    </li>
  </ul>
</template>

<script>
import Color from './Color.vue'

export default {
  name: 'Pins',
  components: { Color },
  props: {
    pins: {
      type: Array,
      default: ['none', 'none', 'none', 'none']
    }
  }
}
</script>