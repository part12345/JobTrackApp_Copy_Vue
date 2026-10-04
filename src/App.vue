<script setup>
import { computed } from 'vue'
import { useRoute } from 'vue-router'
import BottomNav from './components/BottomNav.vue'

const route = useRoute()

const showNavigation = computed(() => {
  return route.meta.requiresAuth
})
</script>

<template>
  <div class="app-shell">

    <main :class="{ 'with-navigation': showNavigation }">
      <RouterView />
    </main>

    <BottomNav v-if="showNavigation" />

  </div>
</template>