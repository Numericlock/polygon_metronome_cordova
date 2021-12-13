<template>
    <div class="home">
        <div class="metronome">
            <Metronome ref="metronome" :bpm="bpm.bpm" :oneMusicalBar="oneMusical.bar" :soundMusicalBar="soundMusicalBar" />
        </div>
        <div class="metronome-controller">
            <div :class="['playback' ,{'pause': playbackNow}]" @click="suspend()"></div>
            <label>
                <span>bpm</span>
                <input type="number" min="1" class="textbox" :value="bpm.bpm" @input="bpmValidate">
                <input type="range" name="bpm" class="slider" :class="{'danger': bpm.isDanger}" :value="bpm.bpm" @input="bpmValidate" :min="bpm.min" :max="bpm.max">
            </label>
            <label>
                <input type="checkbox" v-model="soundMusicalBar" />
                <input type="number" min="1" class="textbox" :value="oneMusical.bar" @input="oneMusicalBarValidate">拍子毎
                <input type="range" name="beat" class="slider" :class="{'danger': oneMusical.isDanger}" :value="oneMusical.bar" @input="oneMusicalBarValidate" :min="oneMusical.min" :max="oneMusical.max">
            </label>
        </div>
    </div>
</template>
<script>
    import Metronome from '../components/Metronome.vue';
    export default {
        name: 'app',
        components: {
            Metronome,
        },
        data() {
            return {
                bpm: {
                    bpm: 60,
                    min: 1,
                    max: 300,
                    isDanger: false
                },
                oneMusical: {
                    bar: 4,
                    min: 2,
                    max: 14,
                    isDanger: false
                },
                musicalBarVals: [2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14],
                vertex: 5,
                soundMusicalBar: true,
                playbackNow: false,
            };
        },
        methods: {
            suspend() {
                if (this.playbackNow) {
                    this.playbackNow = false;
                    this.$refs.metronome.stopMetronome();
                } else {
                    if (this.bpm.bpm) {
                        this.playbackNow = true;
                        this.$refs.metronome.runMetronome();
                    }
                }
            },
            oneMusicalBarValidate: function(e) {
                this.oneMusical.bar = Number(e.target.value);
            },
            bpmValidate: function(e) {
                this.bpm.bpm = Number(e.target.value);
            },

        },
        watch: {
            'oneMusical.bar'() {
                if (this.oneMusical.bar > this.oneMusical.max) this.oneMusical.isDanger = true;
                else this.oneMusical.isDanger = false;
            },
            'bpm.bpm'() {
                if (this.bpm.bpm > this.bpm.max) this.bpm.isDanger = true;
                else this.bpm.isDanger = false;
            }
        }
    };
</script>
<style lang="scss">
    .home {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        font-weight: bold;
        color: white;
        background: linear-gradient(-135deg, #E4A972, #39ACAC),
            linear-gradient(75deg, #E4A972, #9941D8, #79ffff)fixed;
        background: linear-gradient(-135deg, rgba(228, 169, 114, 0.9), rgba(57, 172, 172, 0.9));
        border-bottom-left-radius: 15%;
        border-bottom-right-radius: 15%;
        padding: 10px;

        .metronome-controller {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-around;
            margin-top: 10px;
            padding: 20px 30px;
            height: 150px;
            background: rgba(62, 62, 62, 0.50);
            box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
            backdrop-filter: blur(5.0px);
            -webkit-backdrop-filter: blur(5.0px);
            border-radius: 10px;
            border: 1px solid rgba(255, 255, 255, 0.18);

            .textbox {
                border: none;
                background: none;
                font-weight: bold;
                color: white;
                width: 40px;
            }
        }
    }

    .playback {
        border-top: 15px solid transparent;
        border-bottom: 15px solid transparent;
        border-left: 30px solid #ddd;
        border-radius: 5px;
        transition: border-left 0.1s, border-right 0.1s;
    }

    .pause {
        border-left: 10px solid #ddd;
        border-right: 10px solid #ddd;
        border-top: 0px solid transparent;
        border-bottom: 0px solid transparent;
        border-radius: 0px;
        height: 30px;
        width: 15px;
        transition: border-left 0.1s, border-right 0.1s;
    }

    .slider {
        -webkit-appearance: none;
        appearance: none;
        cursor: pointer;
        background: #ddd;
        height: 3px;
        width: 100%;
        border-radius: 10px;
        border: none;
        outline: 0;

        &:focus {
            box-shadow: 0 0 3px rgb(200, 200, 200);
        }

        &::-webkit-slider-thumb {
            -webkit-appearance: none;
            background: #ddd;
            width: 24px;
            height: 24px;
            border-radius: 50%;
            box-shadow: 0px 3px 6px 0px rgba(0, 0, 0, 0.15);
        }

        &::-moz-range-thumb {
            background: #ddd;
            width: 24px;
            height: 24px;
            border-radius: 50%;
            box-shadow: 0px 3px 6px 0px rgba(0, 0, 0, 0.15);
            border: none;
        }

        &::-moz-focus-outer {
            border: 0;
        }

        &:active::-webkit-slider-thumb {
            box-shadow: 0px 5px 10px -2px rgba(0, 0, 0, 0.3);
        }
    }

    .danger {
        background: red;
    }
</style>