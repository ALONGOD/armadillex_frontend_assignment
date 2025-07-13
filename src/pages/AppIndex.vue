<template>
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

    <!-- Content -->
    <div>
      <LoadingState v-if="isLoading" message="Loading companies..." />

      <ErrorState v-else-if="error" message="Failed to load companies" @retry="handleRetry" />

      <template v-else>
        <!-- Filters -->
        <CompanyFilters :companies="companies" @filtered-companies="handleFilteredCompanies" />

        <!-- Results Summary -->
        <div class="q-mb-md">
          <div class="text-caption text-grey-6">
            Showing {{ filteredCompanies.length }} of {{ companies?.length || 0 }} companies
          </div>
        </div>

        <!-- Table -->
        <CompanyTable :companies="filteredCompanies" />
      </template>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useCompanies } from 'src/composables/useCompanies'
import CompanyTable from 'src/components/CompanyTable.vue'
import CompanyFilters from 'src/components/CompanyFilters.vue'
import LoadingState from 'src/components/LoadingState.vue'
import ErrorState from 'src/components/ErrorState.vue'

const { companies, isLoading, error } = useCompanies()

// Filtered companies state
const filteredCompanies = ref([])

const handleFilteredCompanies = (companies) => {
  filteredCompanies.value = companies
}

const handleRetry = () => {
  window.location.reload()
}
</script>
