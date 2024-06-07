<template>

    <div id="leftnav_layout">
        <div class="profile">
            <slot name="profile"></slot>
        </div>
        <div class="events">
            <slot name="events"></slot>
        </div>
    </div>
</template>

<script lang="ts">
import UserProfile from "../components/UserProfile.vue"

export default {
    name: 'ArticleView',
    components: {
        'UserProfile': UserProfile
    },

}

</script>

<style scoped>
    #leftnav_layout {
        display: grid;
        grid-template-rows: repeat(2, 1fr);
    }
    .profile {
        grid-row: 1 / 2;
    }
    .events {
        grid-row: 2 / 3;
    }
</style>
