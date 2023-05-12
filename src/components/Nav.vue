<script setup lang="ts">
import { RouterLink, useRouter } from 'vue-router';
import Container from './Container.vue'
import AuthModal from './AuthModal.vue'
import { ref } from 'vue';
import { useUsersStore } from '../stores/users'
import { storeToRefs } from 'pinia';

const userStore = useUsersStore();

const { user, loadingUser } = storeToRefs(userStore);
const router = useRouter()
const searchValue = ref<string>('')

const onSearch = () => {
    if (searchValue.value) {
        router.push(`/profile/${searchValue.value}`)
        searchValue.value = ''
    }
}

const handleLogOut = async () => {
    await userStore.handleLogout();
}

const goToUserProfile = () => {
    console.log(user);
    router.push(`/profile/${user.value?.username}`)
}
</script>

<template>
    <a-layout-header>
        <Container>
            <div class="nav-container">
                <div class="left-content">
                    <RouterLink to="/">Instagram</RouterLink>
                    <a-input-search v-model:value="searchValue" placeholder="Username" style="width: 200px"
                        @search="onSearch" />
                </div>
                <div class="content" v-if="!loadingUser">
                    <div class="right-content" v-if="!user">
                        <AuthModal :isLoggedIn="false" />
                        <AuthModal :isLoggedIn="true" />
                    </div>
                    <div class="right-content" v-else>
                        <a-button type="primary" @click="goToUserProfile">Profile</a-button>
                        <a-button type="primary" @click="handleLogOut">Logout</a-button>
                    </div>
                </div>
            </div>
        </Container>
    </a-layout-header>
</template>

<style scoped>
.content {
    display: flex;
    align-items: center;
}

.nav-container {
    display: flex;
    justify-content: space-between;
}

.left-content {
    display: flex;
    align-items: center;
}

.left-content a {
    margin-right: 10px;
}

.right-content {
    display: flex;
    align-items: center;
}

.right-content button {
    margin-left: 10px;
}
</style>