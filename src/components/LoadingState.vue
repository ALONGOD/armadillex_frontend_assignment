<template>
  <div class="text-center q-pa-xl">
    <q-spinner-dots class="text-primary" size="50px" />
    <div class="text-caption text-grey-6 q-mt-md">{{ message }}</div>
  </div>
</template>

<script setup>
defineProps({
  message: {
    type: String,
    default: 'Loading...',
  },
})
</script>
