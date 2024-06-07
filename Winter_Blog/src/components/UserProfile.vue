<!-- 个人信息组件 -->
<template>
    <div id="profile">
        <div id="profile-title">
            <h1>Profile-个人信息</h1>
        </div>
        <div id="info">
            <div id="avatar" style="width: 200px; height: 200px;">
                <img :src=avatarPath style="max-height: 100%; border-radius: 50%; border: 1px solid greenyellow; object-fit: cover;">
            </div>
            <div id="id">
                <a href="https://github.com/bulalalla"> <h3>V.Winter</h3> </a>
                <div style="display: flex; flex-flow: row wrap;">
                    <span>Github ID: </span>
                    <a href="https://github.com/bulalalla"> <p>bulalalla</p> </a>
                    <p>教育背景</p>
                </div>
            </div>
        </div>
    </div>
    
</template>

<script lang="ts">

export default {
    name: "UserProfile",
    props: {
        avatarPath: {
            type: String,
            default: 'https://avatars.githubusercontent.com/u/167631659?s=400&u=312853ed8978522a87134c693fe489a88a1d5eb4&v=4'
        },
        registerDate: {
            type: String,
            default: 'YYYY-MM-DD'
        },
        breifDescription: {
            type: String,
            default: 'xxxxxxxxxxxxx'
        }
    },
    methods: {
        example() {

        }
    },
    mounted: function() {

    } 
}

</script>


<style scoped>

</style>
