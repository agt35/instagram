<script setup lang="ts">
import Container from './Container.vue';
import Cards from './Cards.vue'

import { useUsersStore } from '../stores/users';
import { storeToRefs } from 'pinia';
import LoginMessage from './LoginMessage.vue';

const userStore = useUsersStore();

const { user, loadingUser } = storeToRefs(userStore);


</script>

<template>
    <Container>
        <div v-if="!loadingUser">
            <Cards v-if="user" />
            <LoginMessage v-else />
        </div>
        <div v-else class="spinner">
            <a-spin size="large" />
        </div>
    </Container>
</template>

<style scoped>
.spinner {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 90vh;
}
</style>