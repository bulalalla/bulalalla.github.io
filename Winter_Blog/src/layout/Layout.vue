<template>
    <div class="container" :style="containerStyle">
        <div class="header">
            <slot name="header"></slot>
        </div>
        <div class="leftnav">
            <slot name="leftnav"></slot>
        </div>
        <div class="body">
            <slot name="body"></slot>
        </div>
        <div class="rightnav">
            <slot name="rightnav"></slot>
        </div>
        <div class="footer">
            <slot name="footer"></slot>
        </div>
    </div>
</template>

<script lang="ts">

export default {
    name: 'Layout',
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
        default: '#abc'
        },
        color: {
        type: String,
        default: '#fff'
        }
    },
    computed: {
        containerStyle() {
            return {
                width: this.width,
                height: this.height,
                backgroundColor: this.backgroundColor,
            };
        },
    }
}
</script>

<style scoped>
    .container {
        display: grid;
        grid-template-rows: 60px 1fr 70px;
        grid-template-columns: repeat(10, 1fr);
    }
    .header {
        grid-row: 1 / 2;
        grid-column: 1 / 11;
    }
    .leftnav {
        grid-row: 2 / 3;
        grid-column: 1 / 3;
    }
    .body {
        grid-row: 2 / 3;
        grid-column: 3 / 9;
    }
    .rightnav {
        grid-row: 2 / 3;
        grid-column: 9 / 11;
    }
    .footer {
        grid-row: 3 / 4;
        grid-column: 1 / 11;
    }

</style>
