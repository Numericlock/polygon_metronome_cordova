<template>
    <div :class="{'lists--odd':(vertex%2 == 1) , 'lists':(vertex%2 == 0)}" :style="{'min-width': size + 'px' , 'height' : size + 'px' }">
        <h2>{{ }}</h2>
        <div class="polygon" :class="{'circle-border': border}" :style="{width: size + 'px', height: size + 'px', transform: 'rotate(' + polygonAngle + 'deg)'}">
            <div v-for="n in rectNum" :key="n" class="rect" :style="{width: sideLength + 'px' , height: rectHeight + 'px' , transform: 'rotate(' + centralAngle*n + 'deg)'}"></div>
        </div>
    </div>
    <!--<div>
        <div v-if="isCircumscribed">
            <div v-if="vertex%2 == 1" class="lists--odd">
              <h2>{{ vertex }}</h2>
              <div class="polygon" :class="{'circle-border': border}" :style="{width: size + 'px', height: size + 'px'}">
                <div v-for="k in vertex*2" :key="k" class="rect"
                    :style="{width: circumscribed + 'px' , height:'100%' , transform: 'rotate(' + centralAngle*k + 'deg)'}"></div>
              </div>
            </div>
            <div v-if="vertex%2 == 0" class="lists">
              <h2>{{ vertex }}</h2>
              <div class="polygon" :class="{'circle-border': border}" :style="{width: size + 'px', height: size + 'px', transform: 'rotate(' + centralAngle/2 + 'deg)'}">
                <div v-for="n in vertex/2" :key="n" class="rect"
                    :style="{width: circumscribed + 'px' , height:'100%' , transform: 'rotate(' + centralAngle*n + 'deg)'}"></div>
              </div>
            </div>    
        </div>
        <div v-else>
            <div v-if="vertex%2 == 1" class="lists--odd">
              <h2>{{ vertex }}</h2>
              <div class="polygon" :class="{'circle-border': border}" :style="{width: size + 'px', height: size + 'px'}">
                <div v-for="k in vertex*2" :key="k" class="rect"
                    :style="{width: sideLength + 'px' , height: apothem*2 + 'px' , transform: 'rotate(' + centralAngle*k + 'deg)'}"></div>
              </div>
            </div>
            <div v-if="vertex%2 == 0" class="lists">
              <h2>{{ vertex }}</h2>
              <div class="polygon" :class="{'circle-border': border}" :style="{width: size + 'px', height: size + 'px', transform: 'rotate(' + centralAngle/2 + 'deg)'}">
                <div v-for="n in vertex/2" :key="n" class="rect"
                    :style="{width: sideLength + 'px' , height: apothem*2 + 'px' , transform: 'rotate(' + centralAngle*n + 'deg)'}"></div>
              </div>
            </div>    
        </div>
    </div>
    -->
</template>

<script>
    export default {
        data() {
            return {}
        },
        props: {
            vertex: {
                type: Number,
                default: 5
            },
            size: {
                type: Number,
                default: 400
            },
            isCircumscribed: {
                type: Boolean,
                default: false
            },
            border: {
                type: Boolean,
                default: true
            }
        },
        components: {},
        computed: {
            //辺の長さを求める
            sideLength() {
                let baseAngle, sideLengthResult;
                if (this.isCircumscribed) { //円に外接する場合
                    baseAngle = Math.PI / this.vertex;
                    sideLengthResult = this.size * Math.tan(baseAngle);
                } else { //円に内接する場合
                    baseAngle = 180 / this.vertex;
                    sideLengthResult = this.size * Math.sin(baseAngle * (Math.PI / 180));
                }
                return sideLengthResult;
            },
            //短冊の長さを求める
            rectHeight() {
                let rectHeightResult;
                if (this.isCircumscribed) rectHeightResult = '100%';
                else rectHeightResult = this.apothem * 2;

                return rectHeightResult;
            },
            circumscribed() {
                const baseAngle = Math.PI / this.vertex;
                return this.size * Math.tan(baseAngle);
            },
            apothem() {
                const baseAngle = 180 / this.vertex;
                return (this.size / 2) * Math.cos(baseAngle * (Math.PI / 180));
            },
            centralAngle() {
                return 360 / this.vertex;
            },
            rectNum() {
                let num;
                if (this.vertex % 2 == 0) num = this.vertex / 2;
                else num = this.vertex * 2;
                return num;
            },
            polygonAngle() {
                let result = 0;
                if (this.vertex % 2 == 0) result = this.centralAngle / 2;
                return result;
            },
            circleSize() {
                return {
                    '--circle-size': this.size,
                }
            }
        },
        method: {
            baseAngle() {
                return 180 / this.vertex;
            }
        }
    }
</script>

<style lang="scss">
    @import url(https://fonts.googleapis.com/css?family=Quattrocento+Sans);

    $size: 400px;
    $bk-color: #f5f5f5;
    $border-color: #333;
    $rect-even-color: transparent;
    $rect-odd-color: transparent;
    $text-even-color: #000;
    $text-odd-color: #000;

    @mixin center {
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        margin: auto;
    }

    @mixin center-tf {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }

    @mixin border {
        box-sizing: border-box;
        border: 1px solid $border-color;
        border-left: none;
        border-right: none;
    }

    .list {
        text-align: center;
    }

    .lists,
    .lists--odd {
        position: relative;
        display: inline-block;
        width: 10%;

        h2 {
            font-size: 22px;
            color: $text-even-color;
            font-family: 'Quattrocento Sans';
            z-index: 1;
            @include center-tf;
        }
    }

    .lists--odd {
        h2 {
            color: $text-odd-color;
        }
    }

    .circle-border {
        border: solid 1px $border-color;
    }

    .polygon {
        @include center;

        border-radius: 100%;

        .rect {
            background: $rect-even-color;
            transform-origin: center center;


            @include border;
            @include center;
        }
    }

    .lists--odd {
        .polygon {
            .rect {
                border-top: none
            }
        }
    }
</style>