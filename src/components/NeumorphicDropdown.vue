<script setup lang="ts">
import { computed, nextTick, ref, useTemplateRef } from 'vue'

interface Props {
  options: Option[]
  height?: number
  width?: number
  modelValue?: string | number
}

interface Option {
  text: string
  value: string | number
  selected?: boolean
}

const { options, modelValue } = defineProps<Props>()
const emit = defineEmits<{ 'update:modelValue': [value: string | number] }>()
const expand = ref(false)
const dropdownMenu = useTemplateRef<HTMLElement | null>('dropdown-menu')
const selectedValue = computed(() => options.find(({ value }) => value === modelValue))
const selectedIndex = computed(() => options.findIndex(({ value }) => value === modelValue))

async function onExpand() {
  expand.value = !expand.value

  if (expand.value && dropdownMenu.value && selectedIndex.value !== -1) {
    await nextTick()

    const itemHeight = 50
    const containerHeight = 220
    const scrollPosition = selectedIndex.value * itemHeight - containerHeight / 2 + itemHeight / 2

    dropdownMenu.value.scrollTop = Math.max(0, scrollPosition)
  }
}

function onSelect(value: string | number) {
  emit('update:modelValue', value)
  expand.value = false
}
</script>

<template>
  <div class="dropdown-container" :class="{ expand }">
    <button @click="onExpand">{{ selectedValue?.text }}</button>
    <div class="dropdown-menu-wrapper">
      <div class="dropdown-menu" ref="dropdown-menu">
        <div
          v-for="{ text, value } in options"
          class="dropdown-item"
          :key="value"
          @click="onSelect(value)"
        >
          {{ text }}
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.dropdown-container {
  background: #f1f1f3;
  box-shadow:
    -8px -8px 20px 0px #ffffff,
    4px 4px 20px 0px #dcdcdf;
  color: #030303;
  font-size: 20px;
  border-radius: 40px;
  max-width: 320px;
  height: fit-content;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  transition: max-height 0.3s ease;
}

.dropdown-container:not(.expand) {
  max-height: 60px;
}

.dropdown-container.expand {
  max-height: 280px;
}

.dropdown-container button {
  font-size: 20px;
  font-weight: 500;
  background: #f1f1f3;
  cursor: pointer;
  border: none;
  height: 60px;
  width: 320px;
  border-radius: 40px;
}

.dropdown-menu-wrapper {
  max-height: 0;
  overflow: hidden;
  transition:
    max-height 0.3s ease,
    opacity 0.3s ease;
  opacity: 0;
}

.dropdown-container.expand .dropdown-menu-wrapper {
  max-height: 220px;
  opacity: 1;
}

.dropdown-menu {
  padding: 0 20px 0 20px;
  max-height: 220px;
  overflow-y: scroll;
  overflow-x: hidden;

  -ms-overflow-style: none;
  scrollbar-width: none;
}

.dropdown-menu::-webkit-scrollbar {
  display: none;
}

.dropdown-item {
  display: flex;
  align-items: center;
  height: 50px;
  padding-left: 20px;
  padding-right: 20px;
  border-radius: 40px;
  cursor: pointer;
}

.dropdown-item:hover {
  box-shadow:
    inset 6px 6px 15px 0px #e0e0e0c9,
    inset -8px -9px 15px 0px #ffffff;
}
</style>
