<template>
  <q-dialog v-model="dialog" persistent>
    <q-card style="min-width: 400px; max-width: 90vw">
      <q-card-section>
        <div class="text-h6 text-weight-bold">Create Company</div>
      </q-card-section>
      <q-separator />
      <q-card-section>
        <div v-if="step === 1">
          <q-input
            v-model="companyName"
            label="Company Name"
            autofocus
            :disable="loading"
            @keyup.enter="startAISearch"
          />
        </div>
        <div v-else-if="step === 2" class="q-py-md flex flex-center">
          <q-spinner-dots size="40px" color="primary" />
          <span class="q-ml-md">Generating AI suggestions...</span>
        </div>
        <div v-else-if="step === 3">
          <div class="q-mb-md">AI-powered name suggestions:</div>
          <q-list bordered separator>
            <q-item
              v-for="(suggestion, idx) in suggestions"
              :key="idx"
              clickable
              @click="selectSuggestion(suggestion)"
              :active="selectedSuggestion === suggestion"
            >
              <q-item-section>{{ suggestion }}</q-item-section>
              <q-item-section side v-if="selectedSuggestion === suggestion">
                <q-icon name="check" color="primary" />
              </q-item-section>
            </q-item>
          </q-list>
          <div class="row q-mt-md q-gutter-sm">
            <q-btn flat color="primary" label="Retry" @click="retryAISearch" :disable="loading" />
            <q-btn
              flat
              color="grey"
              label="Skip Suggestions"
              @click="skipSuggestions"
              :disable="loading"
            />
          </div>
        </div>
      </q-card-section>
      <q-separator />
      <q-card-actions align="right">
        <q-btn flat label="Cancel" color="grey" v-close-popup :disable="loading" />
        <q-btn
          color="primary"
          :label="step === 1 ? 'Search' : 'Create'"
          @click="step === 1 ? startAISearch() : createCompany()"
          :disable="loading || (step === 3 && !selectedSuggestion && !skipped)"
        />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script setup>
import { ref, watch } from 'vue'
import { useQuasar } from 'quasar'

const props = defineProps({
  modelValue: Boolean,
})
const emit = defineEmits(['update:modelValue', 'company-created'])

const $q = useQuasar()
const companyName = ref('')
const step = ref(1)
const loading = ref(false)
const suggestions = ref([])
const selectedSuggestion = ref(null)
const skipped = ref(false)
const dialog = ref(false)

// TODO: built in js function called random UUID for creating id here
function generateCompanyId(length = 12) {
  const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
  let result = ''
  for (let i = 0; i < length; i++) {
    result += chars.charAt(Math.floor(Math.random() * chars.length))
  }
  return result
}

watch(
  () => props.modelValue,
  (val) => {
    dialog.value = val
    if (val) reset()
  },
)
watch(dialog, (val) => {
  emit('update:modelValue', val)
})

function reset() {
  companyName.value = ''
  step.value = 1
  loading.value = false
  suggestions.value = []
  selectedSuggestion.value = null
  skipped.value = false
}

function startAISearch() {
  if (!companyName.value.trim()) {
    $q.notify({ type: 'negative', message: 'Please enter a company name.' })
    return
  }
  step.value = 2
  loading.value = true
  setTimeout(() => {
    suggestions.value = generateSuggestions(companyName.value)
    step.value = 3
    loading.value = false
    selectedSuggestion.value = null
    skipped.value = false
  }, 1200)
}

function retryAISearch() {
  startAISearch()
}

function skipSuggestions() {
  skipped.value = true
  selectedSuggestion.value = null
}

function selectSuggestion(suggestion) {
  selectedSuggestion.value = suggestion
  skipped.value = false
}

function createCompany() {
  const name = skipped.value ? companyName.value : selectedSuggestion.value || companyName.value
  const now = new Date()
  const newCompany = {
    id: generateCompanyId(),
    active: true,
    name,
    legalName: name,
    country: 'USA',
    dateAdded: now.toISOString(),
    isDpfFound: false,
    parentId: null,
    providesAiServices: false,
  }
  emit('company-created', newCompany)
  dialog.value = false
  $q.notify({ type: 'positive', message: `Company "${name}" created!` })
}

function generateSuggestions(base) {
  const suffixes = [
    ' Technologies',
    ' Solutions',
    ' Group',
    ' AI',
    ' Labs',
    ' Systems',
    ' Holdings',
  ]
  const baseName = base.replace(
    /\s+(Inc\.|LLC|Ltd|Corp|Corporation|Group|Solutions|Technologies|Labs|Systems|Holdings)$/i,
    '',
  )
  const shuffled = suffixes.sort(() => 0.5 - Math.random())
  return shuffled.slice(0, 3).map((suffix) => baseName + suffix)
}
</script>

<style scoped>
.q-list .q-item {
  cursor: pointer;
}
.q-list .q-item[active] {
  background: #e3f2fd;
}
</style>
