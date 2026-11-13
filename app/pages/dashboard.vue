
<template>
  <v-container fluid class="dashboard-page pa-4 pa-md-5">

    <!-- WELCOME SECTION -->
    <div class="welcome-section mb-6">
      <h2 class="welcome-title">
        Welcome, {{ user.name }}!
      </h2>

      <p class="welcome-subtitle">
        Welcome to SNC SkillBridge. Start sharing your skills
        and discovering new learning opportunities.
      </p>
    </div>

    <!-- ================================= -->
    <!-- STATISTICS CARDS -->
    <!-- ================================= -->

    <v-row class="mb-5">

      <!-- TOTAL SKILLS -->
      <v-col cols="12" sm="6" md="3">
        <v-card class="stat-card" rounded="lg" elevation="2">
          <div class="stat-content">
            <div>
              <div class="stat-label">Total Skills</div>
              <div class="stat-value">
                {{ statistics.totalSkills }}
              </div>
            </div>

            <v-avatar color="#7040C5" size="46">
              <v-icon
                icon="mdi-lightbulb-on"
                color="white"
                size="25"
              />
            </v-avatar>
          </div>
        </v-card>
      </v-col>

      <!-- BEGINNER -->
      <v-col cols="12" sm="6" md="3">
        <v-card class="stat-card" rounded="lg" elevation="2">
          <div class="stat-content">
            <div>
              <div class="stat-label">Beginner</div>
              <div class="stat-value">
                {{ statistics.beginner }}
              </div>
            </div>

            <v-avatar color="#2196F3" size="46">
              <v-icon
                icon="mdi-school"
                color="white"
                size="25"
              />
            </v-avatar>
          </div>
        </v-card>
      </v-col>

      <!-- ADVANCED / EXPERT -->
      <v-col cols="12" sm="6" md="3">
        <v-card class="stat-card" rounded="lg" elevation="2">
          <div class="stat-content">
            <div>
              <div class="stat-label">Advanced / Expert</div>
              <div class="stat-value">
                {{ statistics.advancedExpert }}
              </div>
            </div>

            <v-avatar color="#4CAF50" size="46">
              <v-icon
                icon="mdi-star-circle"
                color="white"
                size="25"
              />
            </v-avatar>
          </div>
        </v-card>
      </v-col>

      <!-- AVAILABLE SKILLS -->
      <v-col cols="12" sm="6" md="3">
        <v-card class="stat-card" rounded="lg" elevation="2">
          <div class="stat-content">
            <div>
              <div class="stat-label">Available Skills</div>
              <div class="stat-value">
                {{ statistics.available }}
              </div>
            </div>

            <v-avatar color="#FF9800" size="46">
              <v-icon
                icon="mdi-check-circle"
                color="white"
                size="25"
              />
            </v-avatar>
          </div>
        </v-card>
      </v-col>

    </v-row>

    <!-- ================================= -->
    <!-- QUICK ACCESS -->
    <!-- ================================= -->

    <div class="section-heading mb-2">
      <h2>Quick Access</h2>
      <p>Quickly access your SkillBridge modules.</p>
    </div>

    <v-row class="mb-5">

      <!-- SKILL RECORDS -->
      <v-col cols="12" md="6">
        <v-card
          class="quick-card"
          rounded="lg"
          elevation="2"
          hover
          @click="navigateTo('/skill-records')"
        >
          <div class="quick-content">

            <v-avatar color="#2196F3" rounded="lg" size="48">
              <v-icon
                icon="mdi-table"
                color="white"
                size="25"
              />
            </v-avatar>

            <div class="quick-text">
              <h4>Skill Records Management</h4>
              <p>
                Manage, organize, and update your registered skills.
              </p>
            </div>

            <v-spacer />

            <v-icon
              icon="mdi-chevron-right"
              color="#08763B"
              size="25"
            />

          </div>
        </v-card>
      </v-col>

      <!-- ACCOUNT SETTINGS -->
      <v-col cols="12" md="6">
        <v-card
          class="quick-card"
          rounded="lg"
          elevation="2"
          hover
          @click="navigateTo('/settings')"
        >
          <div class="quick-content">

            <v-avatar color="#009688" rounded="lg" size="48">
              <v-icon
                icon="mdi-account-cog"
                color="white"
                size="25"
              />
            </v-avatar>

            <div class="quick-text">
              <h4>Account Settings</h4>
              <p>
                View and manage your account information.
              </p>
            </div>

            <v-spacer />

            <v-icon
              icon="mdi-chevron-right"
              color="#08763B"
              size="25"
            />

          </div>
        </v-card>
      </v-col>

    </v-row>

    <!-- ================================= -->
    <!-- SKILL OVERVIEW -->
    <!-- ================================= -->

    <v-row class="mb-5">

      <v-col cols="12" md="6">

        <v-card rounded="lg" elevation="2" class="overview-card">

          <v-card-title class="overview-title">
            Skill Proficiency Overview
          </v-card-title>

          <v-card-subtitle>
            Distribution of your registered skills.
          </v-card-subtitle>

          <v-card-text>

            <div
              v-for="item in proficiencyData"
              :key="item.label"
              class="proficiency-item mb-4"
            >

              <div class="d-flex justify-space-between mb-2">

                <span class="proficiency-label">
                  {{ item.label }}
                </span>

                <strong class="proficiency-count">
                  {{ item.count }}
                </strong>

              </div>

              <v-progress-linear
                :model-value="item.percentage"
                :color="item.color"
                height="9"
                rounded
              />

            </div>

            <div
              v-if="statistics.totalSkills === 0"
              class="empty-overview"
            >
              Add your skills to see your proficiency overview.
            </div>

          </v-card-text>

        </v-card>

      </v-col>

      <!-- SKILL AVAILABILITY -->
      <v-col cols="12" md="6">

        <v-card rounded="lg" elevation="2" class="overview-card">

          <v-card-title class="overview-title">
            Skill Availability
          </v-card-title>

          <v-card-subtitle>
            Overview of your skill availability.
          </v-card-subtitle>

          <v-card-text>

            <div class="availability-row mb-4">

              <div class="availability-icon">
                <v-icon
                  icon="mdi-check-circle"
                  color="#4CAF50"
                  size="30"
                />
              </div>

              <div class="flex-grow-1">
                <div class="availability-label">
                  Available
                </div>
                <div class="availability-description">
                  Skills available for sharing
                </div>
              </div>

              <strong class="availability-number">
                {{ statistics.available }}
              </strong>

            </div>

            <v-divider class="my-3" />

            <div class="availability-row">

              <div class="availability-icon">
                <v-icon
                  icon="mdi-pause-circle"
                  color="#FF9800"
                  size="30"
                />
              </div>

              <div class="flex-grow-1">
                <div class="availability-label">
                  Not Available
                </div>
                <div class="availability-description">
                  Skills currently unavailable
                </div>
              </div>

              <strong class="availability-number">
                {{ statistics.notAvailable }}
              </strong>

            </div>

          </v-card-text>

        </v-card>

      </v-col>

    </v-row>

    <!-- ================================= -->
    <!-- RECENT ACTIVITIES -->
    <!-- ================================= -->

    <v-card rounded="lg" elevation="2" class="activities-card">

      <div class="activities-header">

        <div>
          <h2>Recent Activities</h2>
          <p>Overview of your recent skill records.</p>
        </div>

        <v-btn
          color="success"
          variant="text"
          size="small"
          @click="navigateTo('/skill-records')"
        >
          Manage Skills
          <v-icon end icon="mdi-arrow-right" />
        </v-btn>

      </div>

      <v-divider />

      <v-data-table
        :headers="activityHeaders"
        :items="recentActivities"
        :items-per-page="5"
        density="comfortable"
        hover
        class="activity-table"
        no-data-text="No recent activities available."
      >

        <template #item.activity="{ item }">
          <div class="d-flex align-center ga-2">

            <v-avatar
              color="#E8F5E9"
              size="32"
            >
              <v-icon
                icon="mdi-lightbulb-on-outline"
                color="#4CAF50"
                size="18"
              />
            </v-avatar>

            <span class="font-weight-medium">
              {{ item.activity }}
            </span>

          </div>
        </template>

        <template #item.date="{ item }">
          <span class="text-grey-darken-1">
            {{ formatDate(item.date) }}
          </span>
        </template>

        <template #item.status="{ item }">
          <v-chip
            color="success"
            size="small"
            variant="tonal"
          >
            {{ item.status }}
          </v-chip>
        </template>

        <template #no-data>
          <div class="text-center pa-8">
            <v-icon
              icon="mdi-history"
              size="45"
              color="grey-lighten-1"
            />
            <p class="text-grey mt-2">
              No recent activities yet.
            </p>
          </div>
        </template>

      </v-data-table>

    </v-card>

  </v-container>
</template>

<script setup lang="ts">

import {
  ref,
  computed,
  onMounted,
  onUnmounted
} from 'vue'

// =====================================
// PAGE CONFIGURATION
// =====================================

definePageMeta({
  layout: 'default'
})

// =====================================
// INTERFACES
// =====================================

interface StudentUser {
  id: string
  name: string
  email: string
  picture?: string
}

interface SkillRecord {
  skillId?: string
  id?: string
  userId?: string
  skillName?: string
  category?: string
  description?: string
  proficiency?: string
  availability?: string
  dateAdded?: string
  createdAt?: string
  updatedAt?: string
}

interface ActivityRecord {
  id?: string
  userId?: string
  title?: string
  activity?: string
  description?: string
  message?: string
  date?: string
  createdAt?: string
}

// =====================================
// USER INFORMATION
// =====================================

const user = ref<StudentUser>({
  id: '',
  name: 'Student',
  email: '',
  picture: ''
})

// =====================================
// SKILL RECORDS
// =====================================

const skills = ref<SkillRecord[]>([])

// =====================================
// RECENT ACTIVITIES
// =====================================

const savedActivities = ref<ActivityRecord[]>([])

// =====================================
// LOAD USER
// =====================================

function loadUser() {

  if (!import.meta.client) return

  try {

    const savedUser = sessionStorage.getItem(
      'skillbridge_user'
    )

    if (!savedUser) {
      navigateTo('/login', { replace: true })
      return
    }

    const parsed = JSON.parse(savedUser)

    user.value = {
      id: String(parsed.id || ''),
      name: parsed.name || 'Student',
      email: parsed.email || '',
      picture: parsed.picture || ''
    }

    if (!user.value.id) {
      navigateTo('/login', { replace: true })
    }

  } catch (error) {

    console.error('Unable to load user:', error)

    navigateTo('/login', { replace: true })

  }

}

// =====================================
// SAFE STORAGE READER
// =====================================

function readStorage(key: string): any[] {

  try {

    const data = JSON.parse(
      localStorage.getItem(key) || '[]'
    )

    return Array.isArray(data) ? data : []

  } catch (error) {

    console.error(`Error reading ${key}:`, error)

    return []

  }

}

// =====================================
// LOAD SKILLS AND ACTIVITIES
// =====================================

function loadDashboardData() {

  if (!import.meta.client || !user.value.id) return

  const currentUserId = String(user.value.id)

  // LOAD SKILLS
  const allSkills = readStorage('skills')

  skills.value = allSkills.filter(
    (skill: SkillRecord) =>
      String(skill.userId || '') === currentUserId
  )

  // LOAD ACTIVITIES
  const allActivities = readStorage('recentActivities')

  savedActivities.value = allActivities.filter(
    (activity: ActivityRecord) =>
      !activity.userId ||
      String(activity.userId) === currentUserId
  )

}

// =====================================
// STATISTICS
// =====================================

const statistics = computed(() => {

  const total = skills.value.length

  const beginner = skills.value.filter(
    skill =>
      String(skill.proficiency || '')
        .trim()
        .toLowerCase() === 'beginner'
  ).length

  const advancedExpert = skills.value.filter(
    skill => {

      const level = String(
        skill.proficiency || ''
      ).trim().toLowerCase()

      return level === 'advanced' || level === 'expert'

    }
  ).length

  const available = skills.value.filter(
    skill =>
      String(skill.availability || '')
        .trim()
        .toLowerCase() === 'available'
  ).length

  const notAvailable = total - available

  return {
    totalSkills: total,
    beginner,
    advancedExpert,
    available,
    notAvailable
  }

})

// =====================================
// PROFICIENCY OVERVIEW
// =====================================

const proficiencyData = computed(() => {

  const levels = [
    {
      label: 'Beginner',
      color: '#2196F3',
      count: skills.value.filter(
        skill =>
          String(skill.proficiency || '')
            .toLowerCase() === 'beginner'
      ).length
    },
    {
      label: 'Intermediate',
      color: '#FF9800',
      count: skills.value.filter(
        skill =>
          String(skill.proficiency || '')
            .toLowerCase() === 'intermediate'
      ).length
    },
    {
      label: 'Advanced',
      color: '#4CAF50',
      count: skills.value.filter(
        skill =>
          String(skill.proficiency || '')
            .toLowerCase() === 'advanced'
      ).length
    },
    {
      label: 'Expert',
      color: '#7040C5',
      count: skills.value.filter(
        skill =>
          String(skill.proficiency || '')
            .toLowerCase() === 'expert'
      ).length
    }
  ]

  return levels.map(item => ({
    ...item,
    percentage: statistics.value.totalSkills > 0
      ? (item.count / statistics.value.totalSkills) * 100
      : 0
  }))

})

// =====================================
// ACTIVITY TABLE HEADERS
// =====================================

const activityHeaders = [
  {
    title: 'Activity',
    key: 'activity',
    sortable: true
  },
  {
    title: 'Description',
    key: 'description',
    sortable: false
  },
  {
    title: 'Date',
    key: 'date',
    sortable: true
  },
  {
    title: 'Status',
    key: 'status',
    sortable: false
  }
]

// =====================================
// RECENT ACTIVITIES
// =====================================

const recentActivities = computed(() => {

  // USE SAVED ACTIVITIES IF AVAILABLE
  if (savedActivities.value.length > 0) {

    return savedActivities.value
      .map((activity, index) => ({
        id: activity.id || `ACT-${index}`,
        activity:
          activity.title ||
          activity.activity ||
          'Recent Activity',
        description:
          activity.description ||
          activity.message ||
          'Student activity',
        date:
          activity.date ||
          activity.createdAt ||
          '',
        status: 'Recorded'
      }))
      .sort((a, b) =>
        new Date(b.date).getTime() -
        new Date(a.date).getTime()
      )
      .slice(0, 10)

  }

  // FALLBACK: USE SKILL RECORDS
  return skills.value
    .map((skill, index) => ({
      id: skill.skillId || skill.id || `SKILL-${index}`,
      activity: 'Skill Registration',
      description:
        `${skill.skillName || 'New skill'} was registered.`,
      date:
        skill.dateAdded ||
        skill.createdAt ||
        '',
      status: 'Completed'
    }))
    .sort((a, b) =>
      new Date(b.date).getTime() -
      new Date(a.date).getTime()
    )
    .slice(0, 10)

})

// =====================================
// FORMAT DATE
// =====================================

function formatDate(date: string) {

  if (!date) return 'Recently'

  const parsed = new Date(date)

  if (Number.isNaN(parsed.getTime())) {
    return 'Recently'
  }

  return parsed.toLocaleDateString('en-PH', {
    year: 'numeric',
    month: 'short',
    day: 'numeric'
  })

}

// =====================================
// REFRESH DASHBOARD
// =====================================

function refreshDashboard() {

  loadDashboardData()

}

// =====================================
// LISTEN TO STORAGE UPDATES
// =====================================

function handleStorageUpdate() {

  refreshDashboard()

}

// =====================================
// ON MOUNT
// =====================================

onMounted(() => {

  loadUser()

  loadDashboardData()

  window.addEventListener(
    'storage',
    handleStorageUpdate
  )

  window.addEventListener(
    'skillbridge:data-updated',
    handleStorageUpdate
  )

  window.addEventListener(
    'focus',
    handleStorageUpdate
  )

})

// =====================================
// ON UNMOUNT
// =====================================

onUnmounted(() => {

  window.removeEventListener(
    'storage',
    handleStorageUpdate
  )

  window.removeEventListener(
    'skillbridge:data-updated',
    handleStorageUpdate
  )

  window.removeEventListener(
    'focus',
    handleStorageUpdate
  )

})

</script>

<style scoped>

/* =====================================
   DASHBOARD PAGE
===================================== */

.dashboard-page {
  background: #ffffff;
  min-height: calc(100vh - 56px);
}

/* =====================================
   WELCOME SECTION
===================================== */

.welcome-title {
  font-size: 21px;
  font-weight: 800;
  color: #182b20;
  margin-bottom: 10px;
}

.welcome-subtitle {
  color: #78847d;
  font-size: 14px;
  margin: 0;
}

/* =====================================
   STATISTICS CARDS
===================================== */

.stat-card {
  background: #E8F5E9;
  border: 1px solid #c8dfca;
  min-height: 120px;
  transition: 0.2s ease;
}

.stat-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 5px 12px rgba(0, 0, 0, 0.12) !important;
}

.stat-content {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 18px;
  height: 120px;
}

.stat-label {
  font-size: 13px;
  color: #08763B;
  margin-bottom: 13px;
}

.stat-value {
  font-size: 24px;
  font-weight: 800;
  color: #08763B;
}

/* =====================================
   SECTION HEADING
===================================== */

.section-heading h2 {
  font-size: 19px;
  font-weight: 800;
  color: #182b20;
  margin-bottom: 8px;
}

.section-heading p {
  font-size: 13px;
  color: #78847d;
}

/* =====================================
   QUICK ACCESS
===================================== */

.quick-card {
  background: #E8F5E9;
  border: 1px solid #c8dfca;
  cursor: pointer;
  min-height: 100px;
  transition: 0.2s ease;
}

.quick-card:hover {
  background: #D9EEDD;
  transform: translateY(-2px);
}

.quick-content {
  display: flex;
  align-items: center;
  gap: 15px;
  padding: 18px;
  min-height: 100px;
}

.quick-text {
  flex: 1;
  min-width: 0;
}

.quick-text h4 {
  font-size: 14px;
  font-weight: 800;
  color: #08763B;
  margin-bottom: 7px;
}

.quick-text p {
  font-size: 12px;
  color: #276640;
  margin: 0;
  line-height: 1.5;
}

/* =====================================
   OVERVIEW CARDS
===================================== */

.overview-card {
  border: 1px solid #e0e8e1;
  min-height: 250px;
}

.overview-title {
  color: #08763B;
  font-size: 17px;
  font-weight: 800;
  padding-bottom: 5px;
}

.empty-overview {
  text-align: center;
  color: #9aa49d;
  font-size: 12px;
  padding: 10px;
}

.proficiency-label {
  font-size: 13px;
  color: #52665a;
}

.proficiency-count {
  font-size: 13px;
  color: #08763B;
}

.availability-row {
  display: flex;
  align-items: center;
  gap: 13px;
}

.availability-icon {
  display: flex;
  align-items: center;
  justify-content: center;
}

.availability-label {
  color: #24563a;
  font-size: 14px;
  font-weight: 700;
}

.availability-description {
  color: #8a978d;
  font-size: 11px;
  margin-top: 4px;
}

.availability-number {
  color: #08763B;
  font-size: 20px;
}

/* =====================================
   RECENT ACTIVITIES
===================================== */

.activities-card {
  border: 1px solid #e0e8e1;
  overflow: hidden;
}

.activities-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 12px;
  padding: 20px;
}

.activities-header h2 {
  font-size: 19px;
  color: #182b20;
  font-weight: 800;
  margin-bottom: 7px;
}

.activities-header p {
  font-size: 12px;
  color: #78847d;
  margin: 0;
}

.activity-table {
  font-size: 12px;
}

.activity-table :deep(th) {
  color: #31553c !important;
  font-weight: 700 !important;
  background: #f5faf5;
}

.activity-table :deep(td) {
  color: #455b4c;
}

/* =====================================
   RESPONSIVE
===================================== */

@media (max-width: 600px) {

  .dashboard-page {
    padding: 12px !important;
  }

  .welcome-title {
    font-size: 19px;
  }

  .stat-content {
    padding: 14px;
  }

  .activities-header {
    padding: 14px;
  }

  .activities-header h2 {
    font-size: 16px;
  }

}

</style>
