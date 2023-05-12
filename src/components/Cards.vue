<script setup lang="ts">
import Card from './Card.vue';
import Observer from './Observer.vue';
import { supabase } from '../supabase';
import { useUsersStore } from '@/stores/users';
import { storeToRefs } from 'pinia';
import { onMounted, ref } from 'vue';

const userStore = useUsersStore();
const { user } = storeToRefs(userStore);

const posts = ref([]);
const lastCardIndex = ref(2);
const ownerIds = ref([]);
const reachedEnd = ref(false);

const fetchData = async () => {
    const { data: following } = await supabase
        .from('followers_following')
        .select("following_id")
        .eq('follower_id', user.value?.id)

    ownerIds.value = following?.map(f => f.following_id)

    const { data } = await supabase
        .from('posts')
        .select('*')
        .in('owner_id', ownerIds.value)
        .range(0, lastCardIndex.value)
        .order("created_at", { ascending: false })

    posts.value = data
}

const fetchNextSet = async () => {
    if (!reachedEnd.value) {

        const { data } = await supabase
            .from('posts')
            .select('*')
            .in('owner_id', ownerIds.value)
            .range(lastCardIndex.value + 1, lastCardIndex.value + 3)
            .order("created_at", { ascending: false })

        //push new posts into Array
        console.log(data)
        posts.value = [...posts.value, ...data]

        lastCardIndex.value += 3

        if (!data.length) {
            reachedEnd.value = true;
        }
    }


}

onMounted(() => {
    fetchData()
})
</script>

<template>
    <div class="timeline-container">
        <Card v-for="post in posts" :key="post.id" :post="post" />
        <Observer v-if="posts.length" @intersect="fetchNextSet" />
    </div>
</template>

<style scoped>
.timeline-container {
    padding: 20px 0;
    display: flex;
    flex-direction: column;
    align-items: center;
}
</style>