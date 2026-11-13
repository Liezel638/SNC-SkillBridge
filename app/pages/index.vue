
<template>
  <div class="homepage">
    <v-progress-circular
      indeterminate
      color="success"
      size="40"
      width="4"
    />
    <p>Redirecting to SNC SkillBridge...</p>
  </div>
</template>

<script setup lang="ts">
await navigateTo('/login', {
  replace: true
})
</script>

<style scoped>
.homepage {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 16px;
  background: #f5f3ff;
  color: #087b45;
  font-family: Arial, sans-serif;
}

.homepage p {
  font-size: 14px;
  font-weight: 500;
}
</style>
