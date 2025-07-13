<template>
  <q-card flat bordered class="bg-white">
    <q-table
      :rows="companies"
      :columns="columns"
      row-key="id"
      flat
      bordered
      dense
      class="company-table"
    >
      <!-- Custom header -->
      <template v-slot:header="props">
        <q-tr :props="props">
          <q-th
            v-for="col in props.cols"
            :key="col.name"
            :props="props"
            class="bg-grey-2 text-weight-medium text-grey-8 q-pa-md"
          >
            {{ col.label }}
          </q-th>
        </q-tr>
      </template>

      <!-- Custom rows -->
      <template v-slot:body="props">
        <q-tr
          :props="props"
          class="cursor-pointer"
          :class="{ 'bg-grey-1': props.selected }"
          @click="handleRowClick(props.row)"
        >
          <q-td key="name" :props="props" class="q-pa-md">
            <div>
              <div class="text-weight-medium text-grey-9">{{ props.row.name }}</div>
              <div class="text-caption text-grey-6 q-mt-xs">{{ props.row.legalName }}</div>
            </div>
          </q-td>
          <q-td key="status" :props="props" class="q-pa-md text-center">
            <q-chip
              :color="props.row.active ? 'positive' : 'negative'"
              text-color="white"
              size="sm"
              :label="props.row.active ? 'Active' : 'Inactive'"
            />
          </q-td>
          <q-td key="country" :props="props" class="q-pa-md">
            <div class="row items-center no-wrap">
              <q-icon name="public" class="q-mr-sm text-grey-6" />
              <span class="text-grey-9">{{ props.row.country }}</span>
            </div>
          </q-td>
          <q-td key="aiServices" :props="props" class="q-pa-md text-center">
            <q-icon
              name="memory"
              :color="props.row.providesAiServices ? 'blue' : 'grey-4'"
              size="20px"
            />
          </q-td>
          <q-td key="dpfFound" :props="props" class="q-pa-md text-center">
            <q-icon
              name="security"
              :color="props.row.isDpfFound ? 'orange' : 'grey-4'"
              size="20px"
            />
          </q-td>
          <q-td key="dateAdded" :props="props" class="q-pa-md">
            <span class="text-grey-9">{{ formatDate(props.row.dateAdded) }}</span>
          </q-td>
        </q-tr>
      </template>

      <!-- Empty state -->
      <template v-slot:no-data>
        <div class="text-center q-pa-xl">
          <q-icon name="business" class="text-grey-4" size="50px" />
          <div class="text-h6 text-grey-6 q-mt-md">No companies found</div>
        </div>
      </template>
    </q-table>
  </q-card>
</template>

<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

// Props
defineProps({
  companies: {
    type: Array,
    required: true,
  },
})

// Table columns
const columns = [
  {
    name: 'name',
    label: 'Company Name',
    align: 'left',
    field: 'name',
    sortable: true,
    style: 'min-width: 250px',
  },
  {
    name: 'status',
    label: 'Status',
    align: 'center',
    field: 'active',
    sortable: true,
    style: 'width: 100px',
  },
  {
    name: 'country',
    label: 'Country',
    align: 'left',
    field: 'country',
    sortable: true,
    style: 'width: 100px',
  },
  {
    name: 'aiServices',
    label: 'AI Services',
    align: 'center',
    field: 'providesAiServices',
    sortable: true,
    style: 'width: 100px',
  },
  {
    name: 'dpfFound',
    label: 'DPF Found',
    align: 'center',
    field: 'isDpfFound',
    sortable: true,
    style: 'width: 100px',
  },
  {
    name: 'dateAdded',
    label: 'Date Added',
    align: 'left',
    field: 'dateAdded',
    sortable: true,
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
