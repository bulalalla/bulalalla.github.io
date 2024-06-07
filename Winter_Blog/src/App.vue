<template>
    <!-- my-layout 为 grid 实现的 “回”字型布局，有头、左、中、右、下五个部分 -->
    <!-- 使用插槽可以将组件插入到相应位置，具名插槽有：header、leftnav、body、rightnav、footer，详见./layout/Layout.vue -->
    <my-layout> 
        <!-- header -->
        <template v-slot:header>
            <my-header> aaa </my-header>
        </template>
        <!-- leftnav -->
        <template v-slot:leftnav>
            <ArticleView>
                <template v-slot:profile>
                    <user-profile></user-profile>
                </template>
            </ArticleView> 
        </template>

        <BodyView>
            
        </BodyView>
        <template v-slot:footer>
            <my-footer> bbb </my-footer>
        </template>
    </my-layout>

</template>

<script lang="ts">
import MainView from "./views/MainView.vue"
import ArticleView from "./views/ArticleView.vue"
import Layout from "./layout/Layout.vue"
import HeaderView from "./layout/Header.vue"
import BodyView from "./layout/Body.vue"
import FooterView from "./layout/Footer.vue"
import UserProfileVue from './components/UserProfile.vue'

export default {
    name: 'App', 
    components: {
        'MainVue': MainView,
        'ArticleView': ArticleView,
        'my-layout': Layout,
        'BodyView': BodyView,
        'my-header': HeaderView,
        'my-footer': FooterView,
        'user-profile': UserProfileVue,
    },

}

</script>

<style scoped>
    .body #app {
        width: 100%;
        height: 100%;
    }
</style>
