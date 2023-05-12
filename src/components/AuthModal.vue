<script setup lang="ts">
import { reactive, ref } from 'vue';
import { useUsersStore } from '../stores/users'
import { storeToRefs } from 'pinia';

const userStore = useUsersStore();

const { errorMessage, loading, user } = storeToRefs(userStore); //this makes it so our component rerenders on change to errorMessage, but functions cannot be here

const props = defineProps<{
    isLoggedIn: boolean
}>()

const userCredentials = reactive({
    email: '',
    password: '',
    username: ''
})

const visible = ref<boolean>(false);

const showModal = () => {
    visible.value = true;
};

const handleCancel = () => {
    userStore.clearErrorMessage();
    visible.value = false;
}

const clearUserCredentialsInput = () => {
    userCredentials.email = "";
    userCredentials.password = "";
    userCredentials.username = "";
    userStore.clearErrorMessage();
}

const handleOk = async (e: MouseEvent) => {
    if (props.isLoggedIn) {
        await userStore.handleLogin({
            password: userCredentials.password,
            email: userCredentials.email
        })
    } else {
        await userStore.handleSignup(userCredentials) //since we used storeToRefs we need to get functions from the userStore
    }
    if (user.value) {
        clearUserCredentialsInput();
        visible.value = false;
    }
};

const title = props.isLoggedIn ? 'Login' : 'SignUp'



</script>

<template>
    <div>
        <a-button type="primary" @click="showModal" class="btn">{{ title }}</a-button>
        <a-modal v-model:visible="visible" :title="title" @ok="handleOk">
            <template #footer>
                <a-button key="back" @click="handleCancel">Cancel</a-button>
                <a-button :disabled="loading" key="submit" type="primary" :loading="loading"
                    @click="handleOk">Submit</a-button>
            </template>
            <div v-if="!loading" class="inputs-container">
                <a-input class="input" v-if="!isLoggedIn" v-model:value="userCredentials.username" placeholder="Username" />
                <a-input class="input" v-model:value="userCredentials.email" placeholder="Email" />
                <a-input class="input" v-model:value="userCredentials.password" placeholder="Password" type="password" />
            </div>
            <div v-else>
                <a-spin></a-spin>
            </div>
            <a-typography-text v-if="errorMessage" type="danger">{{ errorMessage }}</a-typography-text>
        </a-modal>
    </div>
</template>

<style scoped>
.btn {
    margin-left: 10px;
}

.input {
    margin-top: 5px;
    ;
}
</style>
