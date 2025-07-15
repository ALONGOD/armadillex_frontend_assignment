<template>
  <div>
    <LoadingState v-if="isLoading" message="Loading companies..." />
    <ErrorState v-else-if="error" message="Failed to load companies" @retry="handleRetry" />
    <template v-else>
      <div class="row items-center justify-between q-mb-lg">
        <div class="col">
          <h1 class="text-h4 text-weight-bold text-primary q-mb-xs">Companies</h1>
          <p class="text-caption text-grey-6 q-ma-none">Manage your company portfolio</p>
        </div>
        <div class="col-auto">
          <q-btn color="primary" icon="add" label="Create Company" @click="openDialog = true" />
        </div>
      </div>
      <CompanyFilters :companies="companies" @filtered-companies="handleFilteredCompanies" />
      <div class="q-mb-md">
        <div class="text-caption text-grey-6">
          Showing {{ filteredCompanies.length }} of {{ companies?.length || 0 }} companies
        </div>
      </div>
      <CompanyTable :companies="filteredCompanies" />
    </template>
    <CreateCompanyDialog v-model="openDialog" @company-created="handleCompanyCreated" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useCompanies } from 'src/composables/useCompanies'
import CompanyTable from 'src/components/CompanyTable.vue'
import CompanyFilters from 'src/components/CompanyFilters.vue'
import LoadingState from 'src/components/LoadingState.vue'
import ErrorState from 'src/components/ErrorState.vue'
import CreateCompanyDialog from 'src/components/CreateCompanyDialog.vue'
import { useQueryClient } from '@tanstack/vue-query'
import { QUERY_KEYS } from 'src/composables/const'

const { companies, isLoading, error } = useCompanies()
const filteredCompanies = ref([])
const openDialog = ref(false)
const queryClient = useQueryClient()

const handleFilteredCompanies = (companies) => {
  filteredCompanies.value = companies
}
const handleRetry = () => {
  window.location.reload()
}
const handleCompanyCreated = (newCompany) => {
  // Add new company to Vue Query cache
  queryClient.setQueryData([QUERY_KEYS.COMPANIES], (old = []) => [newCompany, ...old])
  openDialog.value = false
}
</script>
