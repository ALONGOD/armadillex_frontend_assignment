<template>
  <q-page-container>
    <div class="q-pa-md q-gutter-md">
      <div class="row items-center justify-between q-mb-lg">
        <div class="col">
          <h1 class="text-h4 text-weight-bold text-primary q-mb-xs">Companies</h1>
          <p class="text-caption text-grey-6 q-ma-none">Manage your company portfolio</p>
        </div>
        <div class="col-auto"></div>
      </div>
      <router-view />
    </div>
  </q-page-container>
</template>

<script setup>
import { QPageContainer } from 'quasar'
</script>
