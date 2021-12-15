<template>
    <div class="hand" :style="{'transform': 'rotate(' + rotate + 'deg)', 'transformOrigin': 'center ' + size + 'px'}" />
</template>

<script>
    export default {
        name: 'Hand',
        props: {
            rotate: {
                type: Number,
                default: 0
            },
            size: {
                type: Number,
                default: 130
            }
        }
    }
</script>

<style scoped>
    .hand {
        background-color: rgba(255, 255, 255, 0.8);
        border-radius: 100%;
        width: 20px;
        height: 20px;
        z-index: 100;
    }
</style>