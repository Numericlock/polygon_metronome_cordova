<template>
    <div class="polygonMetronome-wrapper">
        <div class="handball-wrapper">
            <Hand v-if="beatNow%oneMusicalBar==0 && soundMusicalBar" class="hand-ball" :rotate="beatAngle" size="130" style="background-color:red;" />
            <Hand v-else class="hand-ball" :rotate="beatAngle" size="130" />
        </div>
        <Polygon :vertex="oneMusicalBar" :border="true" size="400" />
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
            }
        },
        components: {
            Hand,
            Polygon
        }
    }
</script>

<style lang="scss">
    .polygonMetronome-wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
        position: relative;

        .handball-wrapper {
            display: flex;
            flex-direction: column;
            align-items: center;
            position: relative;

            .hand-ball {
                position: absolute;
                border-radius: 100%;
                background: red;
                width: 20px;
                height: 20px;
                transform-origin: 10px 210px;
                top: -10px;
                background: rgba(62, 62, 62, 0.50);
                box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
                backdrop-filter: blur(5.0px);
                -webkit-backdrop-filter: blur(5.0px);
                border-radius: 10px;
                border: 1px solid rgba(255, 255, 255, 0.18);
            }
        }
    }
</style>