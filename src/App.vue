<script setup lang="ts">
import Nav from './components/Nav.vue'
import { RouterView } from 'vue-router';
import { onMounted } from 'vue';
import { useUsersStore } from './stores/users'

const userStore = useUsersStore();
onMounted(() => {
  userStore.getUser();
})
</script>

<template>
  <main>
    <Nav />
    <RouterView />
  </main>
</template>

<style scoped></style>
