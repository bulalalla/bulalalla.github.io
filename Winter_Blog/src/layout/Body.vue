<template>
    <body :style="bodyStyle" class="my-body">
        <slot></slot>
    </body>
</template>

<script lang="ts">
export default {
    name: 'BodyView',
    props: {
        width: {
        type: String,
        default: '100%'
        },
        height: {
        type: String,
        default: '100%'
        },
        backgroundColor: {
        type: String,
        default: '#fff'
        },
        color: {
        type: String,
        default: '#fff'
        }
    },
    computed: {
        bodyStyle() {
            return {
                display: 'flex',
                'flex-flow': 'row nowrap',
                width: this.width,
                height: this.height,
                backgroundColor: this.backgroundColor,
            };
        },
    }
}
</script>

<style scoped>

</style>
