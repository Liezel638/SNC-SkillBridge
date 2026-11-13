
<template>
  <v-container fluid class="skill-records-page">

    <!-- PAGE HEADER -->
    <div class="page-header">
      <div>
        <h2>Skill Records Management</h2>
        <p>Manage, organize, and update your registered skills.</p>
      </div>

      <v-btn
        color="success"
        prepend-icon="mdi-plus"
        rounded="lg"
        @click="addSkill"
      >
        Add Skill
      </v-btn>
    </div>

    <!-- SUMMARY CARDS -->
    <v-row class="summary-row">

      <v-col cols="12" sm="4">
        <v-card class="summary-card" elevation="1" rounded="lg">
          <div class="summary-content">
            <div>
              <p>Total Skills</p>
              <h2>{{ records.length }}</h2>
            </div>

            <v-avatar color="#7040C5" size="45">
              <v-icon icon="mdi-lightbulb-on" color="white" />
            </v-avatar>
          </div>
        </v-card>
      </v-col>

      <v-col cols="12" sm="4">
        <v-card class="summary-card" elevation="1" rounded="lg">
          <div class="summary-content">
            <div>
              <p>Beginner</p>
              <h2>{{ countProficiency('Beginner') }}</h2>
            </div>

            <v-avatar color="#2196F3" size="45">
              <v-icon icon="mdi-school-outline" color="white" />
            </v-avatar>
          </div>
        </v-card>
      </v-col>

      <v-col cols="12" sm="4">
        <v-card class="summary-card" elevation="1" rounded="lg">
          <div class="summary-content">
            <div>
              <p>Advanced / Expert</p>
              <h2>{{ advancedCount }}</h2>
            </div>

            <v-avatar color="#4CAF50" size="45">
              <v-icon icon="mdi-star-circle" color="white" />
            </v-avatar>
          </div>
        </v-card>
      </v-col>

    </v-row>

    <!-- TOOLBAR -->
    <v-card class="table-card" elevation="1" rounded="lg">

      <div class="table-toolbar">

        <div>
          <h3>My Skill Records</h3>
          <p>Click a cell to edit its information.</p>
        </div>

        <div class="toolbar-actions">

          <v-text-field
            v-model="searchQuery"
            placeholder="Search skills..."
            prepend-inner-icon="mdi-magnify"
            density="compact"
            variant="outlined"
            rounded="lg"
            hide-details
            clearable
            class="search-field"
          />

          <v-btn
            color="error"
            variant="outlined"
            prepend-icon="mdi-delete"
            :disabled="!hasSelection"
            @click="deleteSelected"
          >
            Delete
          </v-btn>

          <v-btn
            color="success"
            prepend-icon="mdi-content-save"
            @click="saveRecords"
          >
            Save
          </v-btn>

        </div>

      </div>

      <v-divider />

      <!-- HANDSONTABLE -->
      <div class="handsontable-container">

        <ClientOnly>
          <HotTable
            ref="hotRef"
            :settings="hotSettings"
          />

          <template #fallback>
            <div class="loading-table">
              <v-progress-circular
                indeterminate
                color="success"
              />
              <p>Loading skill records...</p>
            </div>
          </template>
        </ClientOnly>

      </div>

      <!-- TABLE FOOTER -->
      <div class="table-footer">
        <span>
          {{ records.length }} skill record(s)
        </span>

        <span>
          <v-icon
            icon="mdi-information-outline"
            size="small"
            color="success"
          />
          Changes are saved in your browser.
        </span>
      </div>

    </v-card>

    <!-- ADD SKILL DIALOG -->
    <v-dialog
      v-model="addDialog"
      max-width="520"
      persistent
    >
      <v-card rounded="xl">

        <v-card-title class="dialog-title">
          <v-icon icon="mdi-lightbulb-on" color="success" class="mr-2" />
          Add New Skill
        </v-card-title>

        <v-divider />

        <v-card-text class="pa-5">

          <v-form ref="skillForm" @submit.prevent="confirmAddSkill">

            <v-text-field
              v-model="newSkill.skillName"
              label="Skill Name"
              placeholder="e.g. Web Development"
              prepend-inner-icon="mdi-lightbulb-outline"
              variant="outlined"
              density="comfortable"
              :rules="[requiredRule]"
              class="mb-2"
            />

            <v-select
              v-model="newSkill.category"
              label="Category"
              :items="categories"
              prepend-inner-icon="mdi-shape-outline"
              variant="outlined"
              density="comfortable"
              :rules="[requiredRule]"
              class="mb-2"
            />

            <v-textarea
              v-model="newSkill.description"
              label="Skill Description"
              placeholder="Describe your skill..."
              prepend-inner-icon="mdi-text"
              variant="outlined"
              rows="3"
              auto-grow
              class="mb-2"
            />

            <v-select
              v-model="newSkill.proficiency"
              label="Proficiency Level"
              :items="proficiencyLevels"
              prepend-inner-icon="mdi-star-outline"
              variant="outlined"
              density="comfortable"
              :rules="[requiredRule]"
              class="mb-2"
            />

            <v-select
              v-model="newSkill.availability"
              label="Availability"
              :items="availabilityOptions"
              prepend-inner-icon="mdi-calendar-check"
              variant="outlined"
              density="comfortable"
              :rules="[requiredRule]"
            />

          </v-form>

        </v-card-text>

        <v-divider />

        <v-card-actions class="pa-4">
          <v-spacer />

          <v-btn
            variant="text"
            @click="addDialog = false"
          >
            Cancel
          </v-btn>

          <v-btn
            color="success"
            prepend-icon="mdi-plus"
            @click="confirmAddSkill"
          >
            Add Skill
          </v-btn>

        </v-card-actions>

      </v-card>
    </v-dialog>

    <!-- NOTIFICATION -->
    <v-snackbar
      v-model="snackbar"
      :color="snackbarColor"
      location="bottom end"
      timeout="3000"
    >
      {{ snackbarMessage }}
    </v-snackbar>

  </v-container>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, nextTick, watch } from 'vue'
import { HotTable } from '@handsontable/vue3'
import { registerAllModules } from 'handsontable/registry'
import type { GridSettings } from 'handsontable/settings'
import 'handsontable/styles/handsontable.css'
import 'handsontable/styles/ht-theme-main.css'

registerAllModules()

interface SkillRecord {
  skillId: string
  skillName: string
  category: string
  description: string
  proficiency: string
  availability: string
  dateAdded: string
  userId: string
}

const STORAGE_KEY = 'skills'

const hotRef = ref<any>(null)

const records = ref<SkillRecord[]>([])
const searchQuery = ref('')
const addDialog = ref(false)
const hasSelection = ref(false)

const snackbar = ref(false)
const snackbarMessage = ref('')
const snackbarColor = ref('success')

const currentUser = ref<any>(null)

const categories = [
  'Technology',
  'Programming',
  'Design',
  'Multimedia',
  'Business',
  'Communication',
  'Education',
  'Other',
]

const proficiencyLevels = [
  'Beginner',
  'Intermediate',
  'Advanced',
  'Expert',
]

const availabilityOptions = [
  'Available',
  'Not Available',
]

const requiredRule = (value: string) =>
  !!value || 'This field is required.'

const newSkill = ref({
  skillName: '',
  category: '',
  description: '',
  proficiency: 'Beginner',
  availability: 'Available',
})

// SUMMARY COUNTS
function countProficiency(level: string) {
  return records.value.filter(
    record => record.proficiency === level
  ).length
}

const advancedCount = computed(() => {
  return records.value.filter(
    record =>
      record.proficiency === 'Advanced' ||
      record.proficiency === 'Expert'
  ).length
})

// HANDSONTABLE CONFIGURATION
const hotSettings = ref<GridSettings>({
  data: records.value,
  theme: 'ht-theme-main',

  colHeaders: [
    'Skill ID',
    'Skill Name',
    'Category',
    'Description',
    'Proficiency',
    'Availability',
    'Date Added',
  ],

  columns: [
    {
      data: 'skillId',
      type: 'text',
      readOnly: true,
      width: 115,
    },
    {
      data: 'skillName',
      type: 'text',
      width: 170,
    },
    {
      data: 'category',
      type: 'dropdown',
      source: categories,
      strict: true,
      width: 145,
    },
    {
      data: 'description',
      type: 'text',
      width: 230,
    },
    {
      data: 'proficiency',
      type: 'dropdown',
      source: proficiencyLevels,
      strict: true,
      width: 140,
    },
    {
      data: 'availability',
      type: 'dropdown',
      source: availabilityOptions,
      strict: true,
      width: 145,
    },
    {
      data: 'dateAdded',
      type: 'text',
      readOnly: true,
      width: 120,
    },
  ],

  rowHeaders: true,
  height: 430,
  width: '100%',
  stretchH: 'all',

  manualColumnResize: true,
  manualRowResize: true,

  dropdownMenu: true,
  filters: true,
  columnSorting: true,
  contextMenu: true,

  minSpareRows: 0,
  minSpareCols: 0,

  selectionMode: 'multiple',
  outsideClickDeselects: false,

  autoWrapRow: true,
  autoWrapCol: true,

  licenseKey: 'non-commercial-and-evaluation',

  afterSelectionEnd: () => {
    hasSelection.value = true
  },

  afterDeselect: () => {
    hasSelection.value = false
  },

  afterChange: (changes, source) => {
    if (!changes || source === 'loadData') return

    saveRecords()
  },

  afterRemoveRow: () => {
    saveRecords()
  },
})

// LOAD RECORDS
function loadRecords() {
  if (!import.meta.client) return

  const saved = localStorage.getItem(STORAGE_KEY)

  let allRecords: SkillRecord[] = []

  try {
    allRecords = saved ? JSON.parse(saved) : []

    if (!Array.isArray(allRecords)) {
      allRecords = []
    }
  } catch {
    allRecords = []
  }

  records.value = allRecords.filter(
    record => record.userId === currentUser.value?.id
  )

  hotSettings.value.data = records.value
}

// SAVE RECORDS
function saveRecords() {
  if (!import.meta.client || !currentUser.value?.id) return

  const hot = hotRef.value?.hotInstance

  const currentRecords = hot
    ? hot.getSourceData() as SkillRecord[]
    : records.value

  const saved = localStorage.getItem(STORAGE_KEY)

  let allRecords: SkillRecord[] = []

  try {
    allRecords = saved ? JSON.parse(saved) : []

    if (!Array.isArray(allRecords)) {
      allRecords = []
    }
  } catch {
    allRecords = []
  }

  // Keep records belonging to other users
  const otherUsersRecords = allRecords.filter(
    record => record.userId !== currentUser.value.id
  )

  // Save current user's updated records
  const myRecords = currentRecords.map(record => ({
    ...record,
    userId: currentUser.value.id,
  }))

  localStorage.setItem(
    STORAGE_KEY,
    JSON.stringify([
      ...otherUsersRecords,
      ...myRecords,
    ])
  )

  records.value = myRecords
}

// ADD SKILL
function addSkill() {
  newSkill.value = {
    skillName: '',
    category: '',
    description: '',
    proficiency: 'Beginner',
    availability: 'Available',
  }

  addDialog.value = true
}

// CONFIRM ADD
async function confirmAddSkill() {
  if (
    !newSkill.value.skillName.trim() ||
    !newSkill.value.category ||
    !newSkill.value.proficiency ||
    !newSkill.value.availability
  ) {
    showMessage('Please complete all required fields.', 'error')
    return
  }

  const newRecord: SkillRecord = {
    skillId: `SK-${Date.now()}`,
    skillName: newSkill.value.skillName.trim(),
    category: newSkill.value.category,
    description: newSkill.value.description.trim(),
    proficiency: newSkill.value.proficiency,
    availability: newSkill.value.availability,
    dateAdded: new Date().toISOString().slice(0, 10),
    userId: currentUser.value.id,
  }

  const updatedRecords = [
    ...records.value,
    newRecord,
  ]

  records.value = updatedRecords
  hotSettings.value.data = updatedRecords

  await nextTick()

  const hot = hotRef.value?.hotInstance

  if (hot) {
    hot.loadData(updatedRecords)
  }

  saveRecords()

  addDialog.value = false

  showMessage('Skill record added successfully!', 'success')
}

// DELETE SELECTED ROWS
function deleteSelected() {
  const hot = hotRef.value?.hotInstance

  if (!hot) return

  const selection = hot.getSelectedLast()

  if (!selection) {
    showMessage('Please select a skill record first.', 'warning')
    return
  }

  const startRow = Math.min(selection[0], selection[2])
  const endRow = Math.max(selection[0], selection[2])

  const confirmed = window.confirm(
    'Are you sure you want to delete the selected skill record(s)?'
  )

  if (!confirmed) return

  hot.alter(
    'remove_row',
    startRow,
    endRow - startRow + 1
  )

  saveRecords()

  hasSelection.value = false

  showMessage('Selected records deleted.', 'success')
}

// SEARCH
watch(searchQuery, async (value) => {
  await nextTick()

  const hot = hotRef.value?.hotInstance

  if (!hot) return

  const searchPlugin = hot.getPlugin('search')

  if (value) {
    searchPlugin.query(value)
  } else {
    searchPlugin.query('')
  }

  hot.render()
})

// NOTIFICATION
function showMessage(
  message: string,
  color = 'success'
) {
  snackbarMessage.value = message
  snackbarColor.value = color
  snackbar.value = true
}

// INITIALIZE
onMounted(async () => {
  const savedUser = sessionStorage.getItem('skillbridge_user')

  if (!savedUser) {
    await navigateTo('/login', { replace: true })
    return
  }

  try {
    currentUser.value = JSON.parse(savedUser)

    if (!currentUser.value?.id) {
      await navigateTo('/login', { replace: true })
      return
    }

    loadRecords()

  } catch {
    await navigateTo('/login', { replace: true })
  }
})
</script>

<style scoped>
.skill-records-page {
  padding: 26px 22px;
  background: #ffffff;
}

/* PAGE HEADER */

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 16px;
  margin-bottom: 24px;
}

.page-header h2 {
  font-size: 22px;
  font-weight: 800;
  color: #202020;
  margin-bottom: 9px;
}

.page-header p {
  font-size: 14px;
  color: #777;
}

/* SUMMARY CARDS */

.summary-row {
  margin-bottom: 12px;
}

.summary-card {
  background: #E8F5E9;
  border: 1px solid #C5E1C8;
}

.summary-content {
  padding: 17px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.summary-content p {
  color: #176B3C;
  font-size: 13px;
  margin-bottom: 8px;
}

.summary-content h2 {
  color: #176B3C;
  font-size: 22px;
  font-weight: 800;
}

/* TABLE CARD */

.table-card {
  border: 1px solid #E0E0E0;
  overflow: hidden;
}

.table-toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 16px;
  padding: 20px;
}

.table-toolbar h3 {
  font-size: 17px;
  font-weight: 800;
  color: #176B3C;
  margin-bottom: 6px;
}

.table-toolbar p {
  font-size: 12px;
  color: #777;
}

.toolbar-actions {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 10px;
}

.search-field {
  width: 220px;
}

/* HANDSONTABLE */

.handsontable-container {
  width: 100%;
  padding: 16px;
  overflow-x: auto;
}

.loading-table {
  min-height: 250px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 15px;
  color: #777;
}

.table-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
  padding: 14px 20px;
  border-top: 1px solid #E0E0E0;
  color: #777;
  font-size: 12px;
}

.table-footer span:last-child {
  display: flex;
  align-items: center;
  gap: 6px;
}

.dialog-title {
  display: flex;
  align-items: center;
  font-size: 19px;
  font-weight: 800;
  padding: 20px;
}

/* RESPONSIVE */

@media (max-width: 700px) {
  .skill-records-page {
    padding: 18px 12px;
  }

  .page-header {
    align-items: flex-start;
    flex-direction: column;
  }

  .table-toolbar {
    align-items: stretch;
    flex-direction: column;
  }

  .toolbar-actions {
    align-items: stretch;
    flex-direction: column;
  }

  .search-field {
    width: 100%;
  }

  .table-footer {
    align-items: flex-start;
    flex-direction: column;
  }
}
</style>
