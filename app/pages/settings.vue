
<template>
  <v-container fluid class="settings-page">

    <!-- PAGE HEADER -->
    <div class="page-header">
      <div>
        <h2>Account Settings</h2>
        <p>Manage your account information and preferences.</p>
      </div>
    </div>

    <!-- PROFILE CARD -->
    <v-card class="profile-card mb-6" elevation="1" rounded="lg">
      <div class="profile-header">
        <div class="d-flex align-center ga-4">

          <v-avatar size="85" color="green-lighten-4">
            <v-img
              v-if="user?.picture"
              :src="user.picture"
              alt="Profile picture"
            />
            <v-icon
              v-else
              icon="mdi-account"
              size="45"
              color="success"
            />
          </v-avatar>

          <div>
            <h3>{{ user?.name || 'Student User' }}</h3>
            <p>{{ user?.email || 'No email available' }}</p>

            <v-chip
              color="success"
              size="small"
              variant="tonal"
              class="mt-2"
            >
              <v-icon start icon="mdi-check-circle" size="small" />
              Google Account Connected
            </v-chip>
          </div>

        </div>
      </div>
    </v-card>

    <!-- PROFILE INFORMATION -->
    <v-card class="settings-card mb-6" elevation="1" rounded="lg">

      <div class="section-header">
        <div class="section-icon profile-icon">
          <v-icon icon="mdi-account-outline" />
        </div>

        <div>
          <h3>Profile Information</h3>
          <p>Your personal account information.</p>
        </div>
      </div>

      <v-divider />

      <v-card-text class="pa-5">

        <v-row>

          <v-col cols="12" md="6">
            <v-text-field
              label="Full Name"
              :model-value="user?.name || ''"
              prepend-inner-icon="mdi-account"
              variant="outlined"
              density="comfortable"
              readonly
              hint="Managed by your Google account"
              persistent-hint
            />
          </v-col>

          <v-col cols="12" md="6">
            <v-text-field
              label="Email Address"
              :model-value="user?.email || ''"
              prepend-inner-icon="mdi-email-outline"
              variant="outlined"
              density="comfortable"
              readonly
              hint="Your connected Google email"
              persistent-hint
            />
          </v-col>

        </v-row>

      </v-card-text>
    </v-card>

    <!-- ACCOUNT INFORMATION -->
    <v-card class="settings-card mb-6" elevation="1" rounded="lg">

      <div class="section-header">
        <div class="section-icon account-icon">
          <v-icon icon="mdi-shield-account-outline" />
        </div>

        <div>
          <h3>Account Information</h3>
          <p>Details about your SNC SkillBridge account.</p>
        </div>
      </div>

      <v-divider />

      <v-list class="account-list" lines="two">

        <v-list-item>
          <template #prepend>
            <v-icon icon="mdi-google" color="#4285F4" size="24" />
          </template>

          <v-list-item-title>
            Sign-in Method
          </v-list-item-title>

          <v-list-item-subtitle>
            Google Authentication
          </v-list-item-subtitle>

          <template #append>
            <v-chip color="success" size="small" variant="tonal">
              Connected
            </v-chip>
          </template>
        </v-list-item>

        <v-divider inset />

        <v-list-item>
          <template #prepend>
            <v-icon
              icon="mdi-school-outline"
              color="#4CAF50"
              size="24"
            />
          </template>

          <v-list-item-title>
            Platform
          </v-list-item-title>

          <v-list-item-subtitle>
            SNC SkillBridge – Student Skill Exchange Platform
          </v-list-item-subtitle>
        </v-list-item>

      </v-list>
    </v-card>

    <!-- PREFERENCES -->
    <v-card class="settings-card mb-6" elevation="1" rounded="lg">

      <div class="section-header">
        <div class="section-icon preference-icon">
          <v-icon icon="mdi-tune" />
        </div>

        <div>
          <h3>Preferences</h3>
          <p>Customize your SkillBridge experience.</p>
        </div>
      </div>

      <v-divider />

      <v-list class="account-list" lines="two">

        <v-list-item>
          <template #prepend>
            <v-icon
              icon="mdi-bell-outline"
              color="#FF9800"
              size="24"
            />
          </template>

          <v-list-item-title>
            Notifications
          </v-list-item-title>

          <v-list-item-subtitle>
            Enable in-app notification preferences.
          </v-list-item-subtitle>

          <template #append>
            <v-switch
              v-model="preferences.notifications"
              color="success"
              hide-details
              density="compact"
              inset
            />
          </template>
        </v-list-item>

        <v-divider inset />

        <v-list-item>
          <template #prepend>
            <v-icon
              icon="mdi-email-fast-outline"
              color="#2196F3"
              size="24"
            />
          </template>

          <v-list-item-title>
            Email Updates
          </v-list-item-title>

          <v-list-item-subtitle>
            Enable your preference for email updates.
          </v-list-item-subtitle>

          <template #append>
            <v-switch
              v-model="preferences.emailUpdates"
              color="success"
              hide-details
              density="compact"
              inset
            />
          </template>
        </v-list-item>

      </v-list>

      <div class="preference-footer">
        <v-icon
          icon="mdi-content-save-check-outline"
          color="success"
          size="small"
        />
        <span>{{ preferenceStatus }}</span>
      </div>

    </v-card>

    <!-- SECURITY -->
    <v-card class="settings-card security-card mb-6" elevation="1" rounded="lg">

      <div class="section-header">
        <div class="section-icon security-icon">
          <v-icon icon="mdi-lock-outline" />
        </div>

        <div>
          <h3>Account Security</h3>
          <p>Manage access to your account.</p>
        </div>
      </div>

      <v-divider />

      <v-card-text class="pa-5">

        <div class="security-info">
          <v-icon
            icon="mdi-google"
            color="#4285F4"
            size="30"
          />

          <div>
            <h4>Google Sign-In</h4>
            <p>
              Your account is authenticated through Google.
              Password management is handled by Google.
            </p>
          </div>
        </div>

        <v-divider class="my-4" />

        <v-btn
          color="error"
          variant="outlined"
          prepend-icon="mdi-logout"
          rounded="lg"
          @click="logout"
        >
          Sign Out
        </v-btn>

      </v-card-text>
    </v-card>

    <!-- FOOTER -->
    <div class="page-footer">
      <p>SNC SkillBridge © 2026</p>
      <span>Student Skill Exchange Platform</span>
    </div>

    <!-- NOTIFICATION -->
    <v-snackbar
      v-model="snackbar"
      color="success"
      location="bottom end"
      timeout="2500"
    >
      {{ snackbarMessage }}
    </v-snackbar>

  </v-container>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from 'vue'

definePageMeta({
  layout: 'default',
})

const user = ref<any>(null)

const preferences = ref({
  notifications: true,
  emailUpdates: false,
})

const preferencesLoaded = ref(false)

const preferenceStatus = ref('Your preferences are saved locally.')

const snackbar = ref(false)
const snackbarMessage = ref('')

// LOAD ACCOUNT AND PREFERENCES
onMounted(() => {
  const savedUser = sessionStorage.getItem('skillbridge_user')

  if (!savedUser) {
    navigateTo('/login', { replace: true })
    return
  }

  try {
    user.value = JSON.parse(savedUser)

    if (!user.value?.id) {
      navigateTo('/login', { replace: true })
      return
    }

    const savedPreferences = localStorage.getItem(
      'skillbridge_preferences'
    )

    if (savedPreferences) {
      preferences.value = {
        ...preferences.value,
        ...JSON.parse(savedPreferences),
      }
    }

    preferencesLoaded.value = true

  } catch (error) {
    console.error('Account loading error:', error)
    navigateTo('/login', { replace: true })
  }
})

// SAVE PREFERENCES AUTOMATICALLY
watch(
  preferences,
  (newPreferences) => {
    if (!preferencesLoaded.value) return

    localStorage.setItem(
      'skillbridge_preferences',
      JSON.stringify(newPreferences)
    )

    preferenceStatus.value = 'Preferences saved successfully.'
  },
  { deep: true }
)

// LOGOUT
function logout() {
  const confirmed = window.confirm(
    'Are you sure you want to sign out?'
  )

  if (!confirmed) return

  sessionStorage.removeItem('skillbridge_user')

  navigateTo('/login', { replace: true })
}
</script>

<style scoped>
.settings-page {
  padding: 26px 22px;
  background: #ffffff;
}

/* HEADER */

.page-header {
  margin-bottom: 24px;
}

.page-header h2 {
  font-size: 22px;
  font-weight: 800;
  color: #202020;
  margin-bottom: 7px;
}

.page-header p {
  font-size: 14px;
  color: #777;
}

/* PROFILE */

.profile-card,
.settings-card {
  border: 1px solid #E0E0E0;
}

.profile-card {
  background: #E8F5E9;
  border-color: #C5E1C8;
}

.profile-header {
  padding: 24px;
}

.profile-header h3 {
  font-size: 20px;
  font-weight: 800;
  color: #174B2F;
  margin-bottom: 5px;
}

.profile-header p {
  font-size: 13px;
  color: #66816D;
}

/* SECTION HEADERS */

.section-header {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 20px;
}

.section-header h3 {
  font-size: 16px;
  font-weight: 800;
  color: #174B2F;
  margin-bottom: 5px;
}

.section-header p {
  font-size: 12px;
  color: #888;
}

.section-icon {
  width: 43px;
  height: 43px;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.profile-icon {
  background: #E3F2FD;
  color: #2196F3;
}

.account-icon {
  background: #E8F5E9;
  color: #4CAF50;
}

.preference-icon {
  background: #FFF3E0;
  color: #FF9800;
}

.security-icon {
  background: #F3E5F5;
  color: #8E24AA;
}

/* ACCOUNT LIST */

.account-list {
  padding: 5px 12px;
}

.account-list :deep(.v-list-item-title) {
  font-size: 14px;
  font-weight: 600;
  color: #263C30;
}

.account-list :deep(.v-list-item-subtitle) {
  font-size: 12px;
  color: #888;
  opacity: 1;
}

/* PREFERENCES */

.preference-footer {
  display: flex;
  align-items: center;
  gap: 7px;
  padding: 0 20px 16px;
  font-size: 12px;
  color: #66816D;
}

/* SECURITY */

.security-info {
  display: flex;
  align-items: flex-start;
  gap: 15px;
}

.security-info h4 {
  font-size: 14px;
  font-weight: 700;
  color: #263C30;
  margin-bottom: 5px;
}

.security-info p {
  font-size: 12px;
  color: #777;
  line-height: 1.6;
}

/* FOOTER */

.page-footer {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 8px;
  padding: 15px 5px;
  color: #888;
  font-size: 12px;
}

/* RESPONSIVE */

@media (max-width: 600px) {
  .settings-page {
    padding: 18px 12px;
  }

  .profile-header {
    padding: 18px;
  }

  .profile-header h3 {
    font-size: 17px;
  }

  .page-footer {
    flex-direction: column;
  }
}
</style>
