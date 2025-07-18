<template>
  <div class="q-mb-md">
    <q-card class="bg-grey-1" flat bordered>
      <q-card-section class="q-pb-sm">
        <div class="text-weight-medium text-grey-8 q-mb-md">Filters</div>
        <div class="row q-col-gutter-md q-mb-md">
          <!-- Search Input -->
          <div class="col-12 col-md-6 col-lg-3">
            <q-input
              v-model="filters.search"
              @update:model-value="debouncedHandleFilterChange"
              dense
              outlined
              placeholder="Search companies..."
              clearable
              bg-color="white"
            >
              <template v-slot:prepend>
                <q-icon name="search" class="text-grey-6" />
              </template>
            </q-input>
          </div>

          <!-- Status Filter -->
          <div class="col-12 col-md-6 col-lg-2">
            <q-select
              v-model="filters.status"
              @update:model-value="handleFilterChange"
              :options="statusOptions"
              dense
              outlined
              label="Status"
              clearable
              emit-value
              map-options
              bg-color="white"
            >
              <template v-slot:prepend>
                <q-icon name="fiber_manual_record" :color="getStatusColor(filters.status)" />
              </template>
            </q-select>
          </div>

          <!-- AI Services Filter -->
          <div class="col-12 col-md-6 col-lg-2">
            <q-select
              v-model="filters.aiServices"
              @update:model-value="handleFilterChange"
              :options="aiServicesOptions"
              dense
              outlined
              label="AI Services"
              clearable
              emit-value
              map-options
              bg-color="white"
            >
              <template v-slot:prepend>
                <q-icon name="memory" class="text-blue" />
              </template>
            </q-select>
          </div>

          <!-- DPF Found Filter -->
          <div class="col-12 col-md-6 col-lg-2">
            <q-select
              v-model="filters.dpfFound"
              @update:model-value="handleFilterChange"
              :options="dpfFoundOptions"
              dense
              outlined
              label="DPF Found"
              clearable
              emit-value
              map-options
              bg-color="white"
            >
              <template v-slot:prepend>
                <q-icon name="security" class="text-orange" />
              </template>
            </q-select>
          </div>

          <!-- Clear Filters -->
          <div class="col-12 col-md-6 col-lg-3">
            <div class="row justify-center">
              <q-btn
                flat
                dense
                color="grey-7"
                icon="delete"
                label="Clear"
                @click="clearFilters"
                :disable="!hasActiveFilters"
                class="q-px-md"
              />
            </div>
          </div>
        </div>

        <!-- Active Filters Display -->
        <div v-if="hasActiveFilters" class="q-mt-md">
          <div class="text-caption text-grey-6 q-mb-sm">Active Filters:</div>
          <div class="active-filters-chips">
            <q-chip
              v-if="filters.search"
              removable
              color="primary"
              text-color="white"
              @remove="filters.search = ''"
              size="sm"
            >
              Search: "{{ filters.search }}"
            </q-chip>
            <q-chip
              v-if="filters.status"
              removable
              :color="filters.status === 'active' ? 'positive' : 'negative'"
              text-color="white"
              @remove="filters.status = null"
              size="sm"
            >
              Status: {{ filters.status === 'active' ? 'Active' : 'Inactive' }}
            </q-chip>
            <q-chip
              v-if="filters.aiServices !== null"
              removable
              color="blue"
              text-color="white"
              @remove="filters.aiServices = null"
              size="sm"
            >
              AI Services: {{ filters.aiServices ? 'Yes' : 'No' }}
            </q-chip>
            <q-chip
              v-if="filters.dpfFound !== null"
              removable
              color="orange"
              text-color="white"
              @remove="filters.dpfFound = null"
              size="sm"
            >
              DPF Found: {{ filters.dpfFound ? 'Yes' : 'No' }}
            </q-chip>
          </div>
        </div>
      </q-card-section>
    </q-card>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import debounce from 'lodash/debounce'

// Props
const props = defineProps({
  companies: {
    type: Array,
    required: true,
  },
})

// Emits
const emit = defineEmits(['filter-companies'])

// Filter state
const filters = ref({
  search: '',
  status: null,
  aiServices: null,
  dpfFound: null,
})

// Filter options
const statusOptions = [
  { label: 'Active', value: 'active' },
  { label: 'Inactive', value: 'inactive' },
]

const aiServicesOptions = [
  { label: 'Yes', value: true },
  { label: 'No', value: false },
]

const dpfFoundOptions = [
  { label: 'Yes', value: true },
  { label: 'No', value: false },
]

// Computed properties
const hasActiveFilters = computed(() => {
  return (
    filters.value.search ||
    filters.value.status !== null ||
    filters.value.aiServices !== null ||
    filters.value.dpfFound !== null
  )
})

const filteredCompanies = computed(() => {
  if (!props.companies) return []

  let filtered = [...props.companies]

  // Search filter
  if (filters.value.search) {
    const searchTerm = filters.value.search.toLowerCase()
    filtered = filtered.filter(
      (company) =>
        company.name.toLowerCase().includes(searchTerm) ||
        company.legalName.toLowerCase().includes(searchTerm),
    )
  }

  // Status filter
  if (filters.value.status !== null) {
    filtered = filtered.filter((company) =>
      filters.value.status === 'active' ? company.active : !company.active,
    )
  }

  // AI Services filter
  if (filters.value.aiServices !== null) {
    filtered = filtered.filter((company) => company.providesAiServices === filters.value.aiServices)
  }

  // DPF Found filter
  if (filters.value.dpfFound !== null) {
    filtered = filtered.filter((company) => company.isDpfFound === filters.value.dpfFound)
  }

  return filtered
})

// Methods
const getStatusColor = (status) => {
  if (status === 'active') return 'positive'
  if (status === 'inactive') return 'negative'
  return 'grey-6'
}

const clearFilters = () => {
  filters.value = {
    search: '',
    status: null,
    aiServices: null,
    dpfFound: null,
  }
  handleFilterChange()
}

// Handler to emit filtered companies
const handleFilterChange = () => {
  emit('filter-companies', filteredCompanies.value)
}

// Debounced handler for search input
const debouncedHandleFilterChange = debounce(handleFilterChange, 300)

onMounted(() => {
  handleFilterChange()
})
</script>

<style scoped>
.clear-btn .q-btn__content {
  gap: 4px; /* Reduce space between icon and label */
}
.active-filters-chips {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  padding-left: 0;
  align-items: center;
}
.active-filters-chips .q-chip {
  margin-left: 0;
  padding-left: 10px;
  padding-right: 10px;
}
</style>
