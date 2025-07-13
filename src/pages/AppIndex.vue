<template>
  <div class="q-pa-md">
    <!-- Header -->
    <div class="row items-center justify-between q-mb-lg">
      <div class="col">
        <h1 class="text-h4 text-weight-bold q-mb-xs text-primary">Companies</h1>
        <p class="text-body2 text-grey-7 q-mb-none">Manage your company portfolio</p>
      </div>
      <div class="col-auto"></div>
    </div>

    <!-- Loading State -->
    <div v-if="isLoading" class="text-center q-pa-xl">
      <q-spinner-dots size="50px" color="primary" />
      <div class="text-grey-6 q-mt-md">Loading companies...</div>
    </div>

    <!-- Error State -->
    <div v-else-if="error" class="text-center q-pa-xl">
      <q-icon name="error_outline" size="50px" color="negative" />
      <div class="text-grey-6 q-mt-md">Failed to load companies</div>
      <q-btn
        color="primary"
        label="Retry"
        @click="() => window.location.reload()"
        class="q-mt-md"
      />
    </div>

    <!-- Company Table -->
    <q-card v-else flat bordered class="bg-white">
      <q-table
        :rows="companies"
        :columns="columns"
        row-key="id"
        flat
        bordered
        class="company-table"
        dense
      >
        <!-- Custom header -->
        <template v-slot:header="props">
          <q-tr :props="props">
            <q-th
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              class="bg-grey-2 text-weight-600 text-grey-8"
            >
              {{ col.label }}
            </q-th>
          </q-tr>
        </template>

        <!-- Custom rows -->
        <template v-slot:body="props">
          <q-tr
            :props="props"
            class="cursor-pointer hover:bg-grey-1"
            @click="handleRowClick(props.row)"
          >
            <q-td key="name" :props="props">
              <div>
                <div class="text-weight-medium text-body1">{{ props.row.name }}</div>
                <div class="text-caption text-grey-6">{{ props.row.legalName }}</div>
              </div>
            </q-td>
            <q-td key="status" :props="props" class="text-center">
              <q-chip
                :color="props.row.active ? 'positive' : 'negative'"
                text-color="white"
                size="sm"
                :label="props.row.active ? 'Active' : 'Inactive'"
              />
            </q-td>
            <q-td key="country" :props="props">
              <div class="row items-center">
                <q-icon name="public" size="16px" class="q-mr-xs" />
                <span class="text-body1">{{ props.row.country }}</span>
              </div>
            </q-td>
            <q-td key="aiServices" :props="props" class="text-center">
              <q-icon
                name="memory"
                :color="props.row.providesAiServices ? 'blue' : 'grey-4'"
                size="20px"
              />
            </q-td>
            <q-td key="dpfFound" :props="props" class="text-center">
              <q-icon
                name="security"
                :color="props.row.isDpfFound ? 'orange' : 'grey-4'"
                size="20px"
              />
            </q-td>
            <q-td key="dateAdded" :props="props">
              <span class="text-body1">{{ formatDate(props.row.dateAdded) }}</span>
            </q-td>
          </q-tr>
        </template>

        <!-- Empty state -->
        <template v-slot:no-data>
          <div class="text-center q-pa-xl">
            <q-icon name="business" size="50px" color="grey-4" />
            <div class="text-h6 text-grey-6 q-mt-md">No companies found</div>
          </div>
        </template>
      </q-table>
    </q-card>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'
import { useCompanies } from 'src/composables/useCompanies'

const router = useRouter()
const { companies, isLoading, error } = useCompanies()

// Table columns
const columns = [
  {
    name: 'name',
    label: 'Company Name',
    align: 'left',
    field: 'name',
    sortable: false,
    style: 'min-width: 250px',
  },
  {
    name: 'status',
    label: 'Status',
    align: 'center',
    field: 'active',
    sortable: false,
    style: 'width: 100px',
  },
  {
    name: 'country',
    label: 'Country',
    align: 'left',
    field: 'country',
    sortable: false,
    style: 'width: 100px',
  },
  {
    name: 'aiServices',
    label: 'AI Services',
    align: 'center',
    field: 'providesAiServices',
    sortable: false,
    style: 'width: 100px',
  },
  {
    name: 'dpfFound',
    label: 'DPF Found',
    align: 'center',
    field: 'isDpfFound',
    sortable: false,
    style: 'width: 100px',
  },
  {
    name: 'dateAdded',
    label: 'Date Added',
    align: 'left',
    field: 'dateAdded',
    sortable: false,
    style: 'width: 120px',
  },
]

// Methods
const formatDate = (dateString) => {
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'short',
    day: 'numeric',
  })
}

const handleRowClick = (company) => {
  router.push(`/company/${company.id}`)
}
</script>

<style lang="scss" scoped>
.company-table {
  .q-table__tbody tr {
    transition: background-color 0.2s ease;
  }
}
</style>
