<script setup lang="ts">
import UploadPhotoModal from './UploadPhotoModal.vue';
import { useRoute } from 'vue-router';
import { useUsersStore } from '../stores/users';
import { storeToRefs } from 'pinia';
import { supabase } from '../supabase';

const userStore = useUsersStore();
const route = useRoute();

const props = defineProps(['user', 'userInfo', 'addNewPost', 'isFollowing', 'updateIsFollowing'])
const { user } = storeToRefs(userStore);

const { username: profileUsername } = route.params;

const followUser = async () => {
    props.updateIsFollowing(true);
    await supabase.from("followers_following").insert({
        follower_id: user.value.id,
        following_id: props.user.id
    })
}

const unfollowUser = async () => {
    props.updateIsFollowing(false);
    //could be non asynchronous since it is done in background
    await supabase.from("followers_following")
        .delete()
        .eq("follower_id", user.value.id)
        .eq("following_id", props.user.id)
}
</script>

<template>
    <div class="userbar-container" v-if="props.user">
        <div class="top-content">
            <a-typography-title :level="2">{{ props.user.username }}</a-typography-title>
            <div v-if="user">
                <UploadPhotoModal v-if="profileUsername === user.username" :addNewPost="addNewPost" />
                <div v-else>
                    <a-button v-if="!props.isFollowing" @click="followUser">Follow</a-button>
                    <a-button v-else @click="unfollowUser">Following</a-button>
                </div>
            </div>

        </div>
        <div class="bottom-content">
            <a-typography-title :level="5">{{ props.userInfo.posts }} posts</a-typography-title>
            <a-typography-title :level="5">{{ props.userInfo.followers }} followers</a-typography-title>
            <a-typography-title :level="5">{{ props.userInfo.following }} following</a-typography-title>
        </div>
    </div>
    <div class="userbar-container" v-else>
        <div class="top-content">
            <a-typography-title :level="2">User not found</a-typography-title>
        </div>
    </div>
</template>

<style scoped>
.userbar-container {
    padding-bottom: 75px;
}

.bottom-content {
    display: flex;
    align-items: center;
}

.bottom-content h5 {
    margin: 0;
    padding: 0;
    margin-right: 30px;
}

.top-content {
    display: flex;
    justify-content: space-between;
    align-items: center;
}
</style>