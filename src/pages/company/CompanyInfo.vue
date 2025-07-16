<template>
  <div class="company-info-flex">
    <div class="company-info-left">
      <!-- Existing content starts -->
      <q-page class="q-pa-none">
        <div class="company-header q-pa-xl" style="border-bottom: 1px solid #eee">
          <q-btn
            flat
            color="primary"
            icon="arrow_back"
            label="Back"
            size="md"
            @click="goBack"
            aria-label="Back to Companies"
            class="q-mb-md no-radius-hover"
          />
          <div class="row items-center q-gutter-xl">
            <div class="col-auto">
              <q-avatar size="80px" class="company-avatar" color="primary" text-color="white">
                <template v-if="company?.logo">
                  <img :src="company.logo" alt="Company Logo" />
                </template>
                <template v-else>
                  {{ getInitials(company?.name) }}
                </template>
              </q-avatar>
            </div>
            <div class="col">
              <div class="text-h3 text-weight-bold text-dark q-mb-xs flex items-center">
                {{ company?.name }}
              </div>
              <div class="text-subtitle1 text-grey-7 q-mb-sm">{{ company?.legalName }}</div>
              <q-chip
                class="q-mb-sm"
                :color="company?.active ? 'positive' : 'negative'"
                text-color="white"
                size="md"
              >
                {{ company?.active ? 'Active' : 'Inactive' }}
              </q-chip>
            </div>
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
            </q-list>
          </div>
        </div>
      </q-page>
      <!-- Existing content ends -->
    </div>
    <div class="company-info-right">
      <img
        src="https://res.cloudinary.com/dpoa9lual/image/upload/v1752698092/Screenshot_2025-07-16_at_23.34.39_qnb2rg.png"
        alt="Corporate Building"
        class="company-bg-image"
      />
    </div>
  </div>
</template>

<script setup>
import { useRoute, useRouter } from 'vue-router'
import { computed } from 'vue'
import { useCompanies } from 'src/composables/useCompanies'

const route = useRoute()
const router = useRouter()
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
  const date = new Date(dateString)
  return `${date.getDate()}/${date.getMonth() + 1}/${date.getFullYear()}`
}

function goBack() {
  router.push('/company')
}

function getInitials(name) {
  if (!name) return ''
  const words = name.split(' ')
  if (words.length === 1) return words[0].slice(0, 2).toUpperCase()
  return (words[0][0] + words[words.length - 1][0]).toUpperCase()
}
</script>

<style scoped>
.company-info-flex {
  display: flex;
  flex-direction: row;
  height: 100vh;
  min-height: 0;
  overflow: hidden;
}
.company-info-left {
  flex: 1 1 50%;
  min-width: 0;
  background: transparent;
  z-index: 1;
  min-height: 0;
  overflow: hidden;
}
.company-info-right {
  flex: 1 1 50%;
  min-width: 0;
  display: flex;
  align-items: stretch;
  justify-content: stretch;
  position: relative;
  z-index: 0;
  min-height: 0;
  overflow: hidden;
}
.company-bg-image {
  flex: 1 1 0%;
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
  min-height: 0;
}
.q-page {
  min-height: 0 !important;
  height: 100% !important;
  overflow-y: auto;
}
.company-header {
  width: auto;
  max-width: 100%;
  min-width: 350px;
  min-height: 120px;
  margin-bottom: 0;
}
.company-header .text-h3 {
  white-space: normal;
  word-break: break-word;
  line-height: 1.1;
  max-width: 100%;
}
.company-avatar {
  box-shadow: 0 2px 8px rgba(44, 62, 80, 0.08);
  font-size: 2.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
}
.company-details-wrapper {
  max-width: 900px;
  margin-left: 0;
  margin-right: auto;
}
.company-details-section {
  border: none;
  box-shadow: none;
  margin-left: 0;
  margin-right: 0;
  padding-left: 0;
  padding-right: 0;
  position: relative;
}
.details-list {
  font-size: 1.25rem;
  border-radius: 8px;
  overflow: visible;
  max-width: fit-content;
  margin-left: 0;
  margin-right: 0;
}
.details-list .q-item__section.details-label {
  flex: 0 0 200px !important; /* increased width for alignment */
  margin-right: 80px;
  text-align: left;
}
.details-list .q-item__section.details-value {
  flex: 0 0 auto !important;
  margin-left: 0 !important;
  justify-content: flex-start !important;
}
.details-list .q-item {
  transition: background 0.2s;
  border-radius: 6px;
  width: calc(100% - 4px);
  margin-left: 2px;
  margin-right: 2px;
  padding-top: 10px;
  padding-bottom: 10px;
}
.details-list .q-item:hover {
  background: #e0e3e6;
  border-radius: 6px;
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

/* Responsive: Hide right image and make left full width on small screens */
@media (max-width: 600px) {
  .company-info-flex {
    flex-direction: column;
  }
  .company-info-left {
    flex: 1 1 100%;
    width: 100%;
    min-width: 0;
    max-width: 100vw;
    padding-left: 8px;
    padding-right: 8px;
  }
  .company-info-right {
    display: none;
  }
  .company-header {
    min-width: 0;
    padding: 20px 8px !important;
  }
  .company-details-wrapper {
    max-width: 100vw;
    padding-left: 0 !important;
    padding-right: 0 !important;
  }
  .details-list .q-item__section.details-label {
    flex: 0 0 100px !important;
    margin-right: 16px;
  }
}

/* Remove border radius for the back button on hover/focus/active */
.no-radius-hover .q-btn__wrapper,
.no-radius-hover .q-focus-helper {
  border-radius: 0 !important;
}
</style>
