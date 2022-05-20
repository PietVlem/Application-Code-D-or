<template>
  <div class="color" :class="`color--${color} ${pin ? 'color--pin' : ''}`">
    <slot></slot>
    {{ color }}
  </div>
</template>

<script>
export default {
  name: 'Color',
  props: {
    color: {
      type: String,
      required: true
    },
    pin: {
      type: Boolean,
      default: false
    }
  }
}
</script>
