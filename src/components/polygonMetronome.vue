<template>
    <div class="polygonMetronome-wrapper">
        <Hand v-if="beatNow%oneMusicalBar==0 && soundMusicalBar" class="hand-ball" :rotate="beatAngle" :size="getHandSize" style="background-color:red;" />
        <Hand v-else class="hand-ball" :rotate="beatAngle" :size="getHandSize"/>
        <Polygon :vertex="oneMusicalBar" :border="true" :size="getCircleSize" />
    </div>
</template>

<script>
    import Hand from './Hand.vue'
    import Polygon from './polygon.vue';
    export default {
        data() {
            return {}
        },
        props: {
            oneMusicalBar: {
                type: Number,
            },
            beatAngle: {
                type: Number,
            },
            beatNow: {
                type: Number,
            },
            soundMusicalBar: {
                type: Boolean,
                default: false
            },
            size : {
                type: Number,
                default: 400,
            },
        },
        components: {
            Hand,
            Polygon
        },
        computed: {
            getHandSize() {
                return ((this.size * 0.85) + 44)  / 2;
            },
            getCircleSize() {
                return this.size * 0.85;
            },
        },
    }
</script>

<style lang="scss">
    .polygonMetronome-wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
        .hand-ball {
            background: rgba(62, 62, 62, 0.50);
            box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
            backdrop-filter: blur(5.0px);
            -webkit-backdrop-filter: blur(5.0px);
            border: 1px solid rgba(255, 255, 255, 0.18);
        }
    }
</style>