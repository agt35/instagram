<script setup lang="ts">
import { ref } from 'vue';
import { supabase } from '../supabase';
import { useUsersStore } from '../stores/users';
import { storeToRefs } from 'pinia';

const userStore = useUsersStore();

const { user } = storeToRefs(userStore);
const props = defineProps(['addNewPost'])

const visible = ref<boolean>(false);
const caption = ref("");
const file = ref<any>(null)
const loading = ref(false);
const errorMessage = ref("");

const showModal = () => {
    visible.value = true;
};

const handleOk = async (e: MouseEvent) => {
    loading.value = true;
    const filename = Math.floor(Math.random() * 10000000000000000000)
    let filePath;
    if (file.value) {
        const { data, error } = await supabase.storage.from("images").upload('public/' + filename, file.value)

        if (error) {
            loading.value = false;
            return errorMessage.value = "Unable to upload image"
        }
        filePath = data.path;
        const res = await supabase.from('posts').insert({
            url: filePath,
            caption: caption.value,
            owner_id: user.value.id,
        })
        console.log(res)
        console.log(caption.value)

    }
    loading.value = false;
    visible.value = false;
    props.addNewPost({
        url: filePath,
        caption: caption.value,
    })
    caption.value = "";
};

const handleUploadChange = (e: Event) => {
    if (e.target?.files[0]) {
        file.value = e.target.files[0];
    }
}


</script>
<template>
    <div>
        <a-button @click="showModal">Upload photo</a-button>
        <a-modal v-model:visible="visible" title="Upload Photo" @ok="handleOk">
            <div v-if="!loading">
                <input type="file" accept=".jpeg,.jpg,.png" @change="handleUploadChange">
                <a-input placeholder="Add your caption..." v-model:value="caption" :maxLength="50" />
                <a-typography-text v-if="errorMessage" type="danger">{{ errorMessage }}</a-typography-text>
            </div>
            <div v-else class="spinner">
                <a-spin />
            </div>
        </a-modal>
    </div>
</template>

<style scoped>
input {
    margin-top: 10px;
}

.spinner {
    display: flex;
    align-items: center;
    justify-content: center;
}
</style>