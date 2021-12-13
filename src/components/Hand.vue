<template>
    <div class="hand" :style="{'transform': 'rotate(' + rotate + 'deg)',circleSize}" />
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
        },
        computed: {
            circleSize() {
                return {
                    '--circle-size': this.size,
                }
            }
        }
    }
</script>

<style scoped>
    .hand {
        background-color: rgba(255, 255, 255, 0.8);
        width: 5px;
        height: 150px;
        z-index: 100;
        transform-origin: 2.5px var(--circle-size)px;
    }
</style>