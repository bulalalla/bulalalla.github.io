<template>
    <left-nav :style="leftnavStyle">
        
        <slot></slot>
    </left-nav>
</template>
  
<script>
export default {
    name: 'LeftNavigation',
    props: {
        width: {
        type: String,
        default: '100%'
        },
        height: {
        type: String,
        default: '60px'
        },
        backgroundColor: {
        type: String,
        default: '#333'
        },
        color: {
        type: String,
        default: '#fff'
        }
    },
    computed: {
        leftnavStyle() {
            return {
                width: this.width,
                height: this.height,
                backgroundColor: this.backgroundColor,
                color: this.color,
                display: 'flex',
                'flex-flow': 'row nowrap',
                alignItems: 'center',
                justifyContent: 'center',
                padding: '0 20px',
                boxSizing: 'border-box'
            };
        }
    }
};
</script>
  
<style scoped>
    header {
        /* 可以添加其他默认样式 */

    }
</style>
  