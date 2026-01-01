<script setup lang="ts">
import { useTemplateRef } from 'vue'

const dialogRef = useTemplateRef<HTMLDialogElement | null>('dialog-ref')

function open() {
  dialogRef.value?.showModal()
}

function close() {
  dialogRef.value?.close()
}

defineExpose({ open, close })
</script>
<template>
  <Teleport to="body">
    <dialog ref="dialog-ref"></dialog>
  </Teleport>
</template>

<style scoped>
dialog {
  animation: fade-out 0.7s ease-out;
}

dialog:open {
  animation: fade-in 0.7s ease-out;
}

dialog:open::backdrop {
  background: black;
  animation: backdrop-fade-in 0.7s ease-out forwards;
}

@keyframes fade-in {
  0% {
    opacity: 0;
    transform: scaleY(0);
    display: none;
  }

  100% {
    opacity: 1;
    transform: scaleY(1);
    display: block;
  }
}

@keyframes fade-out {
  0% {
    opacity: 1;
    transform: scaleY(1);
    display: block;
  }

  100% {
    opacity: 0;
    transform: scaleY(0);
    display: none;
  }
}

@keyframes backdrop-fade-in {
  0% {
    opacity: 0;
  }

  100% {
    opacity: 0.3;
  }
}
</style>
