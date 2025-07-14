<template>
  <q-layout view="lHh Lpr lFf">
    <router-view />
  </q-layout>
</template>

<script setup>
import { onMounted } from 'vue'
import { QLayout } from 'quasar'
import { notifyMsgs, notifyService } from './services/notify.service'

onMounted(() => {
  notifyService.success(notifyMsgs.loginSuccess)
})
</script>
