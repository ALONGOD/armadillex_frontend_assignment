<template>
  <q-card flat bordered class="bg-white">
    <q-table
      :rows="sortedCompanies"
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
            class="bg-grey-2 text-weight-medium text-grey-8 q-pa-md cursor-pointer"
            @click="handleSort(col)"
            style="user-select: none"
          >
            <span>{{ col.label }}</span>
            <span v-if="sortBy === col.name">
              <q-icon v-if="sortDesc" name="arrow_downward" size="16px" class="q-ml-xs" />
              <q-icon v-else name="arrow_upward" size="16px" class="q-ml-xs" />
            </span>
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
            <span class="text-grey-9">{{ props.row.country }}</span>
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
import { ref, computed } from 'vue'

const router = useRouter()

// Props
const props = defineProps({
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
    style: 'min-width: 250px',
  },
  {
    name: 'status',
    label: 'Status',
    align: 'center',
    field: 'active',
    style: 'width: 100px',
  },
  {
    name: 'country',
    label: 'Country',
    align: 'center',
    field: 'country',
    style: 'width: 100px',
  },
  {
    name: 'aiServices',
    label: 'AI Services',
    align: 'center',
    field: 'providesAiServices',
    style: 'width: 100px',
  },
  {
    name: 'dpfFound',
    label: 'DPF Found',
    align: 'center',
    field: 'isDpfFound',
    style: 'width: 100px',
  },
  {
    name: 'dateAdded',
    label: 'Date Added',
    align: 'center',
    field: 'dateAdded',
    style: 'width: 100px',
  },
]

// Sorting state
const sortBy = ref(null)
const sortDesc = ref(false)
// Keep a copy of the original order for restoring
const originalCompanies = computed(() =>
  props.companies.map((c, i) => ({ ...c, __originalIndex: i })),
)

// Sorting logic
const sortedCompanies = computed(() => {
  if (!sortBy.value) {
    // Restore original order
    return originalCompanies.value
  }
  const col = columns.find((c) => c.name === sortBy.value)
  if (!col) return originalCompanies.value
  const field = col.field
  return [...originalCompanies.value].sort((a, b) => {
    let aVal = a[field]
    let bVal = b[field]
    // Custom sort for date
    if (field === 'dateAdded') {
      aVal = new Date(aVal)
      bVal = new Date(bVal)
    }
    // Boolean sort (true first ascending, false first descending)
    if (typeof aVal === 'boolean' && typeof bVal === 'boolean') {
      if (aVal === bVal) return 0
      return aVal ? (sortDesc.value ? -1 : 1) : sortDesc.value ? 1 : -1
    }
    // String/number/date sort
    if (aVal < bVal) return sortDesc.value ? 1 : -1
    if (aVal > bVal) return sortDesc.value ? -1 : 1
    return 0
  })
})

function handleSort(col) {
  if (sortBy.value !== col.name) {
    sortBy.value = col.name
    sortDesc.value = true // first click: descending
  } else if (sortDesc.value) {
    sortDesc.value = false // second click: ascending
  } else {
    sortBy.value = null // third click: remove sort
    sortDesc.value = false
  }
}

// Methods
function formatDate(dateString) {
  const date = new Date(dateString)
  const now = new Date()
  const isToday =
    date.getDate() === now.getDate() &&
    date.getMonth() === now.getMonth() &&
    date.getFullYear() === now.getFullYear()
  const isThisYear = date.getFullYear() === now.getFullYear()
  const pad = (n) => n.toString().padStart(2, '0')
  if (isToday) {
    return `${pad(date.getHours())}:${pad(date.getMinutes())}`
  } else if (isThisYear) {
    return `${pad(date.getDate())}/${pad(date.getMonth() + 1)}`
  } else {
    return `${pad(date.getDate())}/${pad(date.getMonth() + 1)}/${date.getFullYear().toString().slice(-2)}`
  }
}

const handleRowClick = (company) => {
  router.push(`/company/${company.id}`)
}
</script>
