<template>
  <q-page class="q-pa-none bg-white" style="min-height: 100vh; width: 100vw">
    <div class="company-header q-pa-xl row items-center" style="border-bottom: 1px solid #eee">
      <div class="col">
        <div class="text-h3 text-weight-bold text-dark q-mb-xs">{{ company?.name }}</div>
        <div class="text-subtitle1 text-grey-7 q-mb-sm">{{ company?.legalName }}</div>
      </div>
      <div class="col-auto">
        <q-chip :color="company?.active ? 'positive' : 'negative'" text-color="white" size="md">
          {{ company?.active ? 'Active' : 'Inactive' }}
        </q-chip>
      </div>
    </div>
    <div class="q-px-xl q-pt-xl company-details-wrapper">
      <div class="company-details-section q-pb-xl">
        <div class="text-h5 text-weight-bold q-mb-lg">Company Details</div>
        <q-list dense class="details-list">
          <q-item v-if="parentCompany">
            <q-item-section avatar><q-icon name="business" /></q-item-section>
            <q-item-section class="details-label">Parent Company</q-item-section>
            <q-item-section side class="details-value">{{
              parentCompany.legalName || parentCompany.name
            }}</q-item-section>
          </q-item>
          <q-item>
            <q-item-section avatar><q-icon name="public" /></q-item-section>
            <q-item-section class="details-label">Country</q-item-section>
            <q-item-section side class="details-value">{{ company?.country }}</q-item-section>
          </q-item>
          <q-item>
            <q-item-section avatar><q-icon name="event" /></q-item-section>
            <q-item-section class="details-label">Date Added</q-item-section>
            <q-item-section side class="details-value">{{
              formatDate(company?.dateAdded)
            }}</q-item-section>
          </q-item>
          <q-item>
            <q-item-section avatar><q-icon name="memory" /></q-item-section>
            <q-item-section class="details-label">AI Services</q-item-section>
            <q-item-section side class="details-value">{{
              company?.providesAiServices ? 'Yes' : 'No'
            }}</q-item-section>
          </q-item>
          <q-item>
            <q-item-section avatar><q-icon name="security" /></q-item-section>
            <q-item-section class="details-label">DPF Found</q-item-section>
            <q-item-section side class="details-value">{{
              company?.isDpfFound ? 'Yes' : 'No'
            }}</q-item-section>
          </q-item>
          <!-- <q-item>
            <q-item-section avatar><q-icon name="verified_user" /></q-item-section>
            <q-item-section class="details-label">Status</q-item-section>
            <q-item-section side class="details-value">
              <q-chip
                :color="company?.active ? 'positive' : 'negative'"
                text-color="white"
                size="md"
              >
                {{ company?.active ? 'Active' : 'Inactive' }}
              </q-chip>
            </q-item-section>
          </q-item> -->
        </q-list>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { useRoute } from 'vue-router'
import { computed } from 'vue'
import { useCompanies } from 'src/composables/useCompanies'

const route = useRoute()
const companyId = computed(() => route.params.id)
const { companies } = useCompanies()

const company = computed(() => {
  if (!companies.value) return null
  return companies.value.find((c) => String(c.id) === String(companyId.value))
})

const parentCompany = computed(() => {
  if (!companies.value || !company.value?.parentId) return null
  return companies.value.find((c) => String(c.id) === String(company.value.parentId))
})

function formatDate(dateString) {
  if (!dateString) return ''
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'short',
    day: 'numeric',
  })
}
</script>

<style scoped>
.company-header {
  width: 100vw;
  max-width: 100vw;
  min-height: 120px;
  margin-bottom: 0;
  background: #fff;
}
.company-details-wrapper {
  max-width: 900px;
  margin-left: 0;
  margin-right: auto;
}
.company-details-section {
  background: #fff;
  border: none;
  box-shadow: none;
  margin-left: 0;
  margin-right: 0;
  padding-left: 0;
  padding-right: 0;
}
.details-list {
  font-size: 1.25rem;
}
.details-label {
  font-size: 1.15rem;
  font-weight: 500;
  color: #263238;
}
.details-value {
  font-size: 1.15rem;
  font-weight: 600;
  color: #222;
}
@media (max-width: 900px) {
  .company-header {
    flex-direction: column;
    align-items: flex-start !important;
    padding: 32px 16px !important;
  }
  .q-px-xl {
    padding-left: 8px !important;
    padding-right: 8px !important;
  }
  .company-details-wrapper {
    margin-left: 0 !important;
    margin-right: 0 !important;
  }
}
</style>
