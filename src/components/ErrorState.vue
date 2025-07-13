<template>
  <div class="text-center q-pa-xl">
    <q-icon name="error_outline" class="text-negative" size="50px" />
    <div class="text-caption text-grey-6 q-mt-md">{{ message }}</div>
    <q-btn v-if="showRetry" color="primary" label="Retry" @click="handleRetry" class="q-mt-md" />
  </div>
</template>

<script setup>
defineProps({
  message: {
    type: String,
    default: 'An error occurred',
  },
  showRetry: {
    type: Boolean,
    default: true,
  },
})

const emit = defineEmits(['retry'])

const handleRetry = () => {
  emit('retry')
}
</script>
