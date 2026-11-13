
<template>
  <div class="login-wrapper">

    <!-- LEFT SIDE -->
    <section class="hero-section">
      <div class="hero-overlay"></div>

      <div class="hero-content">

        <div class="brand">
          <v-icon icon="mdi-school" size="48" color="white" />

          <div>
            <h1>SNC SkillBridge</h1>
            <p>Student Skill Exchange Platform</p>
          </div>
        </div>

        <div class="hero-message">
          <h2>Learn. Share. Grow.</h2>

          <p>
            Connect with fellow students, exchange skills,
            and unlock new learning opportunities!
          </p>

          <div class="feature">
            <v-icon icon="mdi-handshake" />
            <span>Find Learning Partners</span>
          </div>

          <div class="feature">
            <v-icon icon="mdi-lightbulb-on-outline" />
            <span>Share Your Skills</span>
          </div>

          <div class="feature">
            <v-icon icon="mdi-chart-box-outline" />
            <span>Track Your Progress</span>
          </div>
        </div>

        <div class="hero-footer">
          Learn together. Grow together.
        </div>

      </div>
    </section>

    <!-- RIGHT SIDE -->
    <section class="form-section">

      <v-card class="login-card" elevation="0" rounded="xl">

        <div class="form-heading">
          <h2>Welcome to SNC SkillBridge!</h2>

          <p>
            Sign in to continue your learning journey
          </p>
        </div>

        <div class="login-content">

          <h3>Welcome, Nicolite!</h3>

          <p class="description">
            Use your Google account to access SNC SkillBridge.
          </p>

          <!-- GOOGLE SIGN IN BUTTON -->
          <div class="google-button-wrapper">
            <div ref="googleButton" class="google-button"></div>
          </div>

          <v-alert
            v-if="errorMessage"
            type="error"
            variant="tonal"
            density="compact"
            class="mt-5"
          >
            {{ errorMessage }}
          </v-alert>

          <div class="security-note">
            <v-icon icon="mdi-shield-check-outline" color="success" />
            <span>Secure sign-in powered by Google</span>
          </div>

        </div>

      </v-card>

    </section>

  </div>
</template>


<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'

declare global {
  interface Window {
    google?: any
  }
}

const config = useRuntimeConfig()

const googleButton = ref<HTMLElement | null>(null)
const errorMessage = ref('')
const loading = ref(false)


async function handleGoogleLogin(credential: string) {
  try {
    // Split Google ID token into its three parts
    const parts = credential.split('.')

    if (parts.length !== 3 || !parts[1]) {
      throw new Error('Invalid Google ID token.')
    }

    // Decode Base64URL payload
    const base64 = parts[1]
      .replace(/-/g, '+')
      .replace(/_/g, '/')

    const padded = base64.padEnd(
      Math.ceil(base64.length / 4) * 4,
      '='
    )

    const decoded = atob(padded)

    const bytes = Uint8Array.from(
      decoded,
      char => char.charCodeAt(0)
    )

    const payload = JSON.parse(
      new TextDecoder().decode(bytes)
    )

    if (!payload.sub || !payload.email) {
      throw new Error('Google account information is missing.')
    }

    const user = {
      id: payload.sub,
      name: payload.name || payload.email,
      email: payload.email,
      picture: payload.picture || '',
    }

    // Save temporary user information
    sessionStorage.setItem(
      'skillbridge_user',
      JSON.stringify(user)
    )

    // Redirect to dashboard
    await navigateTo('/dashboard')

  } catch (error) {
    console.error('Google login error:', error)
    errorMessage.value = 'Google sign-in failed.'
  }
}


function initializeGoogle() {
  if (!window.google) {
    errorMessage.value = 'Unable to load Google Sign-In.'
    return
  }

  if (!config.public.googleClientId) {
    errorMessage.value = 'Google Client ID is missing.'
    return
  }

  window.google.accounts.id.initialize({
    client_id: config.public.googleClientId,

    callback: (response: { credential: string }) => {
      handleGoogleLogin(response.credential)
    },
  })

  if (googleButton.value) {
    window.google.accounts.id.renderButton(
      googleButton.value,
      {
        type: 'standard',
        theme: 'outline',
        size: 'large',
        text: 'continue_with',
        shape: 'rectangular',
        width: 350,
        logo_alignment: 'center',
      }
    )
  }
}

let googleScript: HTMLScriptElement | null = null

onMounted(() => {
  if (window.google) {
    initializeGoogle()
    return
  }

  googleScript = document.createElement('script')

  googleScript.src = 'https://accounts.google.com/gsi/client'
  googleScript.async = true
  googleScript.defer = true

  googleScript.onload = initializeGoogle

  googleScript.onerror = () => {
    errorMessage.value = 'Google Sign-In could not be loaded.'
  }

  document.head.appendChild(googleScript)
})

onBeforeUnmount(() => {
  googleScript?.remove()
})

definePageMeta({
  layout: false,
})
</script>


<style scoped>
.login-wrapper {
  display: flex;
  width: 100%;
  min-height: 100vh;
  background: #f7f6ff;
}

/* LEFT SECTION */

.hero-section {
  position: relative;
  width: 50%;
  min-height: 100vh;
  display: flex;
  align-items: center;
  overflow: hidden;

  background:
    linear-gradient(
      120deg,
      rgba(0, 105, 55, 0.95),
      rgba(46, 180, 94, 0.78)
    ),
    url('/campus.jpg') center / cover no-repeat;
}

.hero-overlay {
  position: absolute;
  inset: 0;

  background: linear-gradient(
    135deg,
    rgba(0, 100, 48, 0.25),
    rgba(40, 180, 100, 0.25)
  );
}

.hero-content {
  position: relative;
  z-index: 1;
  width: 100%;
  padding: 7%;
  color: white;
}


.brand {
  display: flex;
  align-items: center;
  gap: 18px;
}

.brand .v-icon {
  flex-shrink: 0;
}

.brand h1 {
  font-size: clamp(25px, 3vw, 42px);
  font-weight: 800;
  line-height: 1.15;
  margin: 0;
}

.brand p {
  font-size: 14px;
  opacity: 0.85;
  line-height: 1.4;
  margin: 5px 0 0;
}


.hero-message {
  margin-top: 55px;
}

.hero-message h2 {
  font-size: clamp(35px, 4vw, 52px);
  font-weight: 900;
  margin-bottom: 24px;
}

.hero-message > p {
  max-width: 450px;
  font-size: 17px;
  line-height: 1.7;
  margin-bottom: 30px;
}

.feature {
  display: flex;
  align-items: center;
  gap: 16px;
  margin: 18px 0;
  font-size: 17px;
  font-weight: 700;
}

.feature .v-icon {
  background: rgba(255,255,255,0.2);
  border-radius: 50%;
  padding: 22px;
  box-sizing: content-box;
}

.hero-footer {
  margin-top: 55px;
  font-size: 14px;
  opacity: 0.85;
}

/* RIGHT SECTION */

.form-section {
  width: 50%;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 40px;

  background:
    radial-gradient(circle at top right, #dedaff, transparent 45%),
    #f7f6ff;
}

.login-card {
  width: 100%;
  max-width: 600px;
  padding: 55px 36px;

  background: white;

  box-shadow:
    0 12px 45px rgba(60, 50, 130, 0.09) !important;
}

.form-heading {
  text-align: center;
  margin-bottom: 55px;
}

.form-heading h2 {
  color: #006c37;
  font-size: clamp(24px, 2.4vw, 34px);
  font-weight: 900;
  margin-bottom: 20px;
}

.form-heading p {
  color: #33334f;
  font-size: 15px;
}

.login-content {
  text-align: center;
}

.login-content h3 {
  font-size: 22px;
  color: #183b2a;
  margin-bottom: 12px;
}

.description {
  color: #666;
  font-size: 14px;
  margin-bottom: 35px;
}

.google-button-wrapper {
  width: 100%;
  display: flex;
  justify-content: center;
}

.google-button {
  width: 350px;
  max-width: 100%;
  display: flex;
  justify-content: center;
}

.security-note {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 8px;
  margin-top: 40px;
  color: #777;
  font-size: 12px;
}

/* RESPONSIVE */

@media (max-width: 900px) {
  .hero-section {
    display: none;
  }

  .form-section {
    width: 100%;
    padding: 20px;
  }

  .login-card {
    max-width: 550px;
  }
}

@media (max-width: 480px) {
  .login-card {
    padding: 35px 20px;
  }

  .form-heading h2 {
    font-size: 25px;
  }
}
</style>
