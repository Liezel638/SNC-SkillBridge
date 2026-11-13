
<template>
  <v-app>
    <v-layout class="app-layout">

      <!-- SIDEBAR -->
      <v-navigation-drawer
        v-model="drawer"
        :temporary="$vuetify.display.mobile"
        :permanent="!$vuetify.display.mobile"
        width="245"
        color="#E8F5E9"
        elevation="0"
        class="sidebar"
      >
        <!-- BRAND -->
        <div class="sidebar-brand">
          <v-avatar color="success" rounded="lg" size="42">
            <v-icon icon="mdi-school" color="white" size="28" />
          </v-avatar>

          <div class="brand-info">
            <h3>SNC SkillBridge</h3>
            <span>Student Skill Exchange Platform</span>
          </div>
        </div>

        <v-divider class="mx-3 my-2" />

        <!-- MAIN MENU -->
        <div class="menu-label">MAIN MENU</div>

        <v-list nav density="comfortable" class="sidebar-list">
          <v-list-item
            title="Dashboard"
            subtitle="Overview of student activities"
            to="/dashboard"
            rounded="lg"
            exact
          >
            <template #prepend>
              <v-icon icon="mdi-view-dashboard-outline" color="#4CAF50" />
            </template>
          </v-list-item>
        </v-list>

        <v-divider class="mx-3 my-3" />

        <!-- DATA MANAGEMENT -->
        <div class="menu-label">DATA MANAGEMENT</div>

        <v-list nav density="comfortable" class="sidebar-list">
          <v-list-item
            title="Skill Records Management"
            subtitle="Manage your skill records"
            to="/skill-records"
            rounded="lg"
          >
            <template #prepend>
              <v-icon icon="mdi-table" color="#2196F3" />
            </template>
          </v-list-item>
        </v-list>

        <v-divider class="mx-3 my-3" />

        <!-- ACCOUNT -->
        <div class="menu-label">ACCOUNT</div>

        <v-list nav density="comfortable" class="sidebar-list">
          <v-list-item
            title="Settings"
            subtitle="Manage account settings"
            to="/settings"
            rounded="lg"
          >
            <template #prepend>
              <v-icon icon="mdi-cog-outline" color="#4CAF50" />
            </template>
          </v-list-item>

          <v-list-item
            title="Logout"
            rounded="lg"
            @click="logout"
          >
            <template #prepend>
              <v-icon icon="mdi-logout" color="#F44336" />
            </template>
          </v-list-item>
        </v-list>

        <!-- SIDEBAR FOOTER -->
        <template #append>
          <div class="sidebar-footer">
            <v-divider class="mb-3" />
            <div class="footer-text">
              SNC SkillBridge © 2026
            </div>
          </div>
        </template>

      </v-navigation-drawer>

      <!-- MAIN CONTENT -->
      <v-main class="main-content">

        <!-- TOP BAR -->
        <v-app-bar
          color="#C8E6C9"
          elevation="0"
          height="56"
          class="top-bar"
        >
          <v-btn
            icon="mdi-menu"
            variant="text"
            aria-label="Toggle navigation"
            @click="drawer = !drawer"
          />

          <v-app-bar-title class="top-title">
            {{ pageTitle }}
          </v-app-bar-title>

          <v-spacer />

          <!-- ================================= -->
          <!-- NOTIFICATION DROPDOWN -->
          <!-- ================================= -->

          <v-menu
            v-model="notificationMenu"
            location="bottom end"
            :close-on-content-click="false"
            :offset="10"
            max-width="390"
            min-width="320"
          >
            <template #activator="{ props }">
              <v-btn
                v-bind="props"
                icon
                variant="text"
                class="notification-button"
                aria-label="Open notifications"
              >
                <v-badge
                  :content="unreadCount"
                  :model-value="unreadCount > 0"
                  color="error"
                  floating
                >
                  <v-icon
                    icon="mdi-bell-outline"
                    color="#08763B"
                    size="25"
                  />
                </v-badge>
              </v-btn>
            </template>

            <v-card
              class="notification-panel"
              rounded="lg"
              elevation="8"
            >

              <!-- HEADER -->
              <div class="notification-header">
                <div>
                  <h3>Notifications</h3>
                  <p>
                    {{ unreadCount }} unread notification(s)
                  </p>
                </div>

                <v-btn
                  variant="text"
                  color="success"
                  size="small"
                  :disabled="unreadCount === 0"
                  @click="markAllAsRead"
                >
                  Mark all as read
                </v-btn>
              </div>

              <v-divider />

              <!-- FILTER TABS -->
              <v-tabs
                v-model="activeNotificationTab"
                color="success"
                density="compact"
                grow
              >
                <v-tab value="all">All</v-tab>
                <v-tab value="skills">Skills</v-tab>
                <v-tab value="activities">Activities</v-tab>
              </v-tabs>

              <v-divider />

              <!-- NOTIFICATION LIST -->
              <div class="notification-list">

                <template v-if="filteredNotifications.length > 0">

                  <button
                    v-for="notification in filteredNotifications"
                    :key="notification.id"
                    type="button"
                    class="notification-item"
                    :class="{
                      'notification-unread': !notification.read
                    }"
                    @click="markAsRead(notification.id)"
                  >

                    <v-avatar
                      :color="notification.color"
                      size="42"
                    >
                      <v-icon
                        :icon="notification.icon"
                        color="white"
                        size="20"
                      />
                    </v-avatar>

                    <span class="notification-details">

                      <span class="notification-title-row">
                        <strong>
                          {{ notification.title }}
                        </strong>

                        <span
                          v-if="!notification.read"
                          class="unread-dot"
                        />
                      </span>

                      <span class="notification-message">
                        {{ notification.message }}
                      </span>

                      <span class="notification-time">
                        {{ formatNotificationDate(notification.date) }}
                      </span>

                    </span>

                  </button>

                </template>

                <!-- EMPTY STATE -->
                <div
                  v-else
                  class="notification-empty"
                >
                  <v-icon
                    icon="mdi-bell-sleep-outline"
                    size="45"
                    color="#A5B5A9"
                  />

                  <h4>No notifications yet</h4>

                  <p>
                    New skill updates and activities will appear here.
                  </p>
                </div>

              </div>

              <v-divider />

              <!-- FOOTER -->
              <div class="notification-footer">
                <span>SNC SkillBridge</span>

                <v-btn
                  variant="text"
                  color="success"
                  size="small"
                  @click="refreshNotifications"
                >
                  <v-icon start icon="mdi-refresh" />
                  Refresh
                </v-btn>
              </div>

            </v-card>
          </v-menu>

          <!-- PROFILE DROPDOWN -->
          <v-menu location="bottom end">

            <template #activator="{ props }">
              <v-btn
                icon
                variant="text"
                v-bind="props"
                class="profile-button"
                aria-label="Profile menu"
              >
                <v-avatar size="36" color="success">
                  <v-img
                    v-if="user.picture"
                    :src="user.picture"
                    alt="Student profile"
                  />

                  <span v-else class="text-white">
                    {{ userInitials }}
                  </span>
                </v-avatar>
              </v-btn>
            </template>

            <v-card min-width="250" rounded="lg">

              <v-list>
                <v-list-item>

                  <template #prepend>
                    <v-avatar color="success">
                      <v-img
                        v-if="user.picture"
                        :src="user.picture"
                        alt="Student profile"
                      />

                      <span v-else class="text-white">
                        {{ userInitials }}
                      </span>
                    </v-avatar>
                  </template>

                  <v-list-item-title class="font-weight-bold">
                    {{ user.name }}
                  </v-list-item-title>

                  <v-list-item-subtitle>
                    {{ user.email }}
                  </v-list-item-subtitle>

                </v-list-item>
              </v-list>

              <v-divider />

              <v-list density="compact">

                <v-list-item
                  title="Settings"
                  to="/settings"
                >
                  <template #prepend>
                    <v-icon
                      icon="mdi-cog-outline"
                      color="#4CAF50"
                    />
                  </template>
                </v-list-item>

                <v-list-item
                  title="Logout"
                  @click="logout"
                >
                  <template #prepend>
                    <v-icon
                      icon="mdi-logout"
                      color="#F44336"
                    />
                  </template>
                </v-list-item>

              </v-list>

            </v-card>
          </v-menu>

        </v-app-bar>

        <!-- PAGE CONTENT -->
        <div class="page-content">
          <slot />
        </div>

      </v-main>
    </v-layout>
  </v-app>
</template>

<script setup lang="ts">

import {
  ref,
  computed,
  onMounted,
  onUnmounted,
  watch
} from 'vue'

import { useRoute } from 'vue-router'

// =====================================
// INTERFACES
// =====================================

interface StudentUser {
  id: string
  name: string
  email: string
  picture?: string
}

interface SkillBridgeNotification {
  id: string
  title: string
  message: string
  type: 'skills' | 'activities'
  icon: string
  color: string
  date: string
  read: boolean
}

// =====================================
// ROUTE AND DRAWER
// =====================================

const route = useRoute()

const drawer = ref(true)

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
// PAGE TITLE
// =====================================

const pageTitle = computed(() => {

  const titles: Record<string, string> = {
    '/dashboard': 'Dashboard',
    '/skill-records': 'Skill Records Management',
    '/settings': 'Settings'
  }

  return titles[route.path] || 'SNC SkillBridge'

})

// =====================================
// USER INITIALS
// =====================================

const userInitials = computed(() => {

  return user.value.name
    .split(' ')
    .filter(Boolean)
    .slice(0, 2)
    .map(part => part.charAt(0).toUpperCase())
    .join('') || 'S'

})

// =====================================
// LOAD GOOGLE USER
// =====================================

function loadUser() {

  if (!import.meta.client) return

  const savedUser = sessionStorage.getItem(
    'skillbridge_user'
  )

  if (!savedUser) {

    if (route.path !== '/login') {
      navigateTo('/login', { replace: true })
    }

    return
  }

  try {

    const parsedUser = JSON.parse(savedUser)

    if (!parsedUser.id || !parsedUser.email) {

      sessionStorage.removeItem('skillbridge_user')

      navigateTo('/login', { replace: true })

      return
    }

    user.value = {
      id: String(parsedUser.id),
      name: parsedUser.name || 'Student',
      email: parsedUser.email,
      picture: parsedUser.picture || ''
    }

  } catch (error) {

    console.error('Unable to load student account:', error)

    sessionStorage.removeItem('skillbridge_user')

    navigateTo('/login', { replace: true })

  }

}

// =====================================
// LOGOUT
// =====================================

async function logout() {

  if (import.meta.client) {
    sessionStorage.removeItem('skillbridge_user')
  }

  user.value = {
    id: '',
    name: 'Student',
    email: '',
    picture: ''
  }

  await navigateTo('/login')

}

// =====================================
// NOTIFICATION SETTINGS
// =====================================

const notificationMenu = ref(false)

const activeNotificationTab = ref('all')

const notifications = ref<SkillBridgeNotification[]>([])

// =====================================
// UNREAD COUNT
// =====================================

const unreadCount = computed(() => {

  return notifications.value.filter(
    item => !item.read
  ).length

})

// =====================================
// FILTER NOTIFICATIONS
// =====================================

const filteredNotifications = computed(() => {

  if (activeNotificationTab.value === 'all') {
    return notifications.value
  }

  return notifications.value.filter(
    item => item.type === activeNotificationTab.value
  )

})

// =====================================
// SAFE LOCAL STORAGE READER
// =====================================

function readStorage(key: string): any[] {

  try {

    const parsed = JSON.parse(
      localStorage.getItem(key) || '[]'
    )

    return Array.isArray(parsed) ? parsed : []

  } catch {

    return []

  }

}

// =====================================
// DATE FORMAT
// =====================================

function getDate(value: any): string {

  if (!value) {
    return new Date().toISOString()
  }

  if (typeof value === 'number') {
    return new Date(value).toISOString()
  }

  if (typeof value === 'string') {

    const parsed = new Date(value)

    return Number.isNaN(parsed.getTime())
      ? new Date().toISOString()
      : parsed.toISOString()

  }

  if (typeof value === 'object' && value.seconds) {

    return new Date(
      value.seconds * 1000
    ).toISOString()

  }

  return new Date().toISOString()

}

// =====================================
// REFRESH NOTIFICATIONS
// =====================================

function refreshNotifications() {

  if (!import.meta.client) return

  const userId = String(user.value.id || '')

  if (!userId) {
    notifications.value = []
    return
  }

  // READ EXISTING DATA
  const skills = readStorage('skills')

  const activities = readStorage('recentActivities')

  // READ NOTIFICATION STATUS
  const readIds = new Set(
    readStorage('skillbridge_read_notifications')
      .map(item => String(item.id))
  )

  const generated: SkillBridgeNotification[] = []

  // =====================================
  // 1. SKILL NOTIFICATIONS
  // =====================================

  skills
    .filter(skill =>
      String(skill.userId || '') === userId
    )
    .forEach(skill => {

      const skillId = String(
        skill.skillId || skill.id || ''
      )

      if (!skillId) return

      const updatedAt = skill.updatedAt || ''

      const createdAt =
        skill.dateAdded ||
        skill.createdAt ||
        skill.date ||
        ''

      const isUpdated = Boolean(updatedAt)

      const dateValue = isUpdated
        ? updatedAt
        : createdAt

      const notificationId =
        `skill-${skillId}-${dateValue || 'new'}`

      generated.push({

        id: notificationId,

        title: isUpdated
          ? 'Skill Updated'
          : 'New Skill Added',

        message: isUpdated
          ? `${skill.skillName || 'A skill'} was updated.`
          : `${skill.skillName || 'A skill'} was added to your skill records.`,

        type: 'skills',

        icon: isUpdated
          ? 'mdi-pencil-circle'
          : 'mdi-lightbulb-on',

        color: '#7040C5',

        date: getDate(dateValue),

        read: readIds.has(notificationId)

      })

    })

  // =====================================
  // 2. RECENT ACTIVITIES
  // =====================================

  activities.forEach(activity => {

    const activityId = String(
      activity.id || activity.activityId || ''
    )

    if (!activityId) return

    // Only show activities belonging to current user
    if (
      activity.userId &&
      String(activity.userId) !== userId
    ) {
      return
    }

    const notificationId =
      `activity-${activityId}`

    const dateValue =
      activity.date ||
      activity.createdAt ||
      ''

    generated.push({

      id: notificationId,

      title:
        activity.title ||
        activity.activity ||
        'Recent Activity',

      message:
        activity.description ||
        activity.message ||
        'You performed an activity.',

      type: 'activities',

      icon: 'mdi-history',

      color: '#2196F3',

      date: getDate(dateValue),

      read: readIds.has(notificationId)

    })

  })

  // =====================================
  // SORT NEWEST FIRST
  // =====================================

  notifications.value = generated.sort(
    (a, b) =>
      new Date(b.date).getTime() -
      new Date(a.date).getTime()
  )

}

// =====================================
// SAVE READ NOTIFICATIONS
// =====================================

function saveReadNotifications() {

  if (!import.meta.client) return

  const readIds = notifications.value
    .filter(item => item.read)
    .map(item => ({
      id: item.id
    }))

  localStorage.setItem(
    'skillbridge_read_notifications',
    JSON.stringify(readIds)
  )

}

// =====================================
// MARK ONE AS READ
// =====================================

function markAsRead(id: string) {

  const notification = notifications.value.find(
    item => item.id === id
  )

  if (!notification) return

  notification.read = true

  saveReadNotifications()

}

// =====================================
// MARK ALL AS READ
// =====================================

function markAllAsRead() {

  notifications.value.forEach(item => {
    item.read = true
  })

  saveReadNotifications()

}

// =====================================
// FORMAT NOTIFICATION DATE
// =====================================

function formatNotificationDate(date: string) {

  const timestamp = new Date(date).getTime()

  if (Number.isNaN(timestamp)) {
    return 'Recently'
  }

  const difference = Math.max(
    0,
    Date.now() - timestamp
  )

  const minutes = Math.floor(
    difference / 60000
  )

  const hours = Math.floor(
    difference / 3600000
  )

  const days = Math.floor(
    difference / 86400000
  )

  if (minutes < 1) return 'Just now'

  if (minutes < 60) {
    return `${minutes} min ago`
  }

  if (hours < 24) {
    return `${hours} hr ago`
  }

  if (days < 7) {
    return `${days} days ago`
  }

  return new Date(date).toLocaleDateString()

}

// =====================================
// NOTIFICATION UPDATE EVENT
// =====================================

function handleNotificationUpdate() {

  refreshNotifications()

}

// =====================================
// WATCH NOTIFICATION MENU
// =====================================

watch(notificationMenu, isOpen => {

  if (isOpen) {
    refreshNotifications()
  }

})

// =====================================
// WATCH ROUTE CHANGES
// =====================================

watch(
  () => route.path,

  path => {

    if (path !== '/login') {

      loadUser()

      refreshNotifications()

    }

  }
)

// =====================================
// ON MOUNT
// =====================================

onMounted(() => {

  loadUser()

  refreshNotifications()

  window.addEventListener(
    'skillbridge:data-updated',
    handleNotificationUpdate
  )

  window.addEventListener(
    'storage',
    handleNotificationUpdate
  )

})

// =====================================
// ON UNMOUNT
// =====================================

onUnmounted(() => {

  window.removeEventListener(
    'skillbridge:data-updated',
    handleNotificationUpdate
  )

  window.removeEventListener(
    'storage',
    handleNotificationUpdate
  )

})

</script>

<style scoped>

/* =====================================
   GENERAL LAYOUT
===================================== */

.app-layout {
  min-height: 100vh;
  background: #fff;
}

/* =====================================
   SIDEBAR
===================================== */

.sidebar {
  border-right: 1px solid #c8dfca;
}

.sidebar-brand {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px;
}

.brand-info {
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 2px;
  min-width: 0;
}

.brand-info h3 {
  margin: 0;
  color: #173c2b;
  font-size: 15px;
  font-weight: 800;
  line-height: 1.2;
  white-space: nowrap;
}

.brand-info span {
  margin: 0;
  color: #66816d;
  font-size: 10px;
  line-height: 1.3;
  white-space: nowrap;
}

/* =====================================
   MENU
===================================== */

.menu-label {
  padding: 8px 12px;
  color: #176b3c;
  font-size: 11px;
  font-weight: 800;
}

.sidebar-list {
  padding: 0 8px;
}

.sidebar-list :deep(.v-list-item) {
  min-height: 48px;
  margin-bottom: 5px;
}

.sidebar-list :deep(.v-list-item:hover) {
  background: #d5ebd8;
}

.sidebar-list :deep(.v-list-item--active) {
  color: #08763b;
  background: #b9dfbd;
}

.sidebar-list :deep(.v-list-item__prepend > .v-icon) {
  opacity: 1;
}

.sidebar-list :deep(.v-list-item-title) {
  font-size: 13px;
  font-weight: 600;
}

.sidebar-list :deep(.v-list-item-subtitle) {
  font-size: 10px;
  line-height: 1.4;
  white-space: normal;
}

/* =====================================
   SIDEBAR FOOTER
===================================== */

.sidebar-footer {
  padding: 12px;
}

.footer-text {
  color: #77917c;
  font-size: 10px;
  text-align: center;
}

/* =====================================
   TOP BAR
===================================== */

.top-bar {
  border-bottom: 1px solid #a5d6a7;
}

.top-title {
  color: #14251a;
  font-size: 18px;
  font-weight: 700;
}

/* =====================================
   NOTIFICATION BUTTON
===================================== */

.notification-button {
  width: 42px;
  height: 42px;
  border-radius: 50%;
}

.notification-button:hover {
  background: #d5ebd8;
}

/* =====================================
   NOTIFICATION PANEL
===================================== */

.notification-panel {
  overflow: hidden;
  border: 1px solid #e0e8e1;
}

.notification-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
  padding: 16px;
}

.notification-header h3 {
  margin: 0 0 4px;
  color: #174b2f;
  font-size: 17px;
  font-weight: 800;
}

.notification-header p {
  margin: 0;
  color: #829087;
  font-size: 11px;
}

/* =====================================
   NOTIFICATION LIST
===================================== */

.notification-list {
  max-height: 370px;
  overflow-y: auto;
}

.notification-item {
  display: flex;
  width: 100%;
  align-items: flex-start;
  gap: 12px;
  padding: 14px 16px;
  border: 0;
  border-bottom: 1px solid #edf1ed;
  background: #fff;
  cursor: pointer;
  text-align: left;
  transition: background 0.2s;
}

.notification-item:hover {
  background: #f0f8f1;
}

.notification-unread {
  background: #f2f9f3;
}

.notification-details {
  display: flex;
  flex: 1;
  min-width: 0;
  flex-direction: column;
  gap: 4px;
}

.notification-title-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
}

.notification-title-row strong {
  color: #244333;
  font-size: 12px;
}

.notification-message {
  color: #77847b;
  font-size: 11px;
  line-height: 1.5;
}

.notification-time {
  color: #9aa49d;
  font-size: 10px;
}

/* =====================================
   UNREAD INDICATOR
===================================== */

.unread-dot {
  width: 8px;
  height: 8px;
  min-width: 8px;
  border-radius: 50%;
  background: #4caf50;
}

/* =====================================
   EMPTY NOTIFICATION
===================================== */

.notification-empty {
  padding: 40px 20px;
  text-align: center;
}

.notification-empty h4 {
  margin: 12px 0 5px;
  color: #31533c;
  font-size: 14px;
}

.notification-empty p {
  margin: 0;
  color: #8a968d;
  font-size: 11px;
  line-height: 1.5;
}

/* =====================================
   NOTIFICATION FOOTER
===================================== */

.notification-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 8px 16px;
  background: #f7fbf7;
}

.notification-footer span {
  color: #66816d;
  font-size: 11px;
  font-weight: 600;
}

/* =====================================
   PROFILE
===================================== */

.profile-button {
  margin-right: 10px;
}

/* =====================================
   PAGE CONTENT
===================================== */

.page-content {
  min-height: calc(100vh - 56px);
  background: #fff;
}

/* =====================================
   RESPONSIVE
===================================== */

@media (max-width: 600px) {

  .brand-info h3 {
    font-size: 14px;
  }

  .top-title {
    font-size: 16px;
  }

}

</style>
