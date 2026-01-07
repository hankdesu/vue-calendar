<script setup lang="ts">
interface Props {
  text?: string
  clickHandler?: () => void
  type?: 'submit' | 'button' | 'reset'
  width?: number
  height?: number
  borderRadius?: string
}

interface Emits {
  click: []
}

const { type = 'button', width = 50, height = 50, borderRadius = '50%' } = defineProps<Props>()
const emit = defineEmits<Emits>()
</script>

<template>
  <button
    :type="type"
    @click.prevent="emit('click')"
    :style="{ width: width + 'px', height: height + 'px', borderRadius }"
  >
    {{ text }}
  </button>
</template>

<style scoped>
button {
  background: #f1f1f3;
  box-shadow:
    -8px -8px 20px 0px #ffffff,
    4px 4px 20px 0px #dcdcdf;
  border: none;
  cursor: pointer;
  color: #333333;
  font-size: 20px;
}

button:active {
  box-shadow:
    inset 2px 2px 10px 0px #e0e0e0c9,
    inset -8px -9px 15px 0px #ffffff;
}
</style>
