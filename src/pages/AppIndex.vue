<template>
  <q-page-container>
    <div class="q-pa-md q-gutter-md">
      <!-- Header -->
      <div class="row items-center justify-between q-mb-lg">
        <div class="col">
          <h1 class="text-h4 text-weight-bold text-primary q-mb-xs">Companies</h1>
          <p class="text-caption text-grey-6 q-ma-none">Manage your company portfolio</p>
        </div>
        <div class="col-auto">
          <!-- Future: Add Company button -->
        </div>
      </div>
      <!-- Child route content -->
      <router-view />
    </div>
  </q-page-container>
</template>

<script setup>
import { QPageContainer } from 'quasar'
// No company data or table logic needed here
</script>
