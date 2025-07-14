<template>
  <div>
    <LoadingState v-if="isLoading" message="Loading companies..." />
    <ErrorState v-else-if="error" message="Failed to load companies" @retry="handleRetry" />
    <template v-else>
      <CompanyFilters :companies="companies" @filtered-companies="handleFilteredCompanies" />
      <div class="q-mb-md">
        <div class="text-caption text-grey-6">
          Showing {{ filteredCompanies.length }} of {{ companies?.length || 0 }} companies
        </div>
      </div>
      <CompanyTable :companies="filteredCompanies" />
    </template>
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
const filteredCompanies = ref([])

const handleFilteredCompanies = (companies) => {
  filteredCompanies.value = companies
}
const handleRetry = () => {
  window.location.reload()
}
</script>
