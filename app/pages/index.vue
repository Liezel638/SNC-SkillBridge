<script setup lang="ts">
// Automatically redirect the homepage to the login page
await navigateTo('/login', {
  replace: true
})
</script>

<template>
  <div class="redirect-page">
    <p>Redirecting to SNC SkillBridge...</p>
  </div>
</template>

<style scoped>
.redirect-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f5f7f5;
  color: #087443;
  font-family: Arial, sans-serif;
}
</style>