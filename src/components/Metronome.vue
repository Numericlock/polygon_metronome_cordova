<template>
    <div class="metronome-wrapper dark">
        <polygonMetronome :oneMusicalBar="oneMusicalBar" :beatAngle="beatAngle" :beatNow="beatNow" :soundMusicalBar="soundMusicalBar" />
    </div>
</template>

<script>
    import polygonMetronome from './polygonMetronome.vue'
    let source;
    let audioContext;
    let base64Text;
    let gainNode;
    export default {
        data() {
            return {
                elapsedTime: 0,
                beat: "",
                isMetronomeRunning: false,
                beatTime: 0,
                swingAngle: 90,
                beatAngle: 0,
                edgeLength: 100,
                vertex: 5,
                beatTimer: ""
            }
        },
        components: {
            polygonMetronome
        },
        props: {
            bpm: {
                type: Number,
                default: 60
            },
            fps: {
                type: Number,
                default: 30
            },
            oneMusicalBar: {
                type: Number,
            },
            soundMusicalBar: {
                type: Boolean,
                default: true
            }
        },
        computed: {
            // pendulumAngle() {
            //     const swingAngle = 90;
            //     const swingProgress = this.elapsedTime % this.oneBeatSec(); //スイングの進捗ミリ秒
            //     const swingProgressRate = (swingProgress / this.oneBeatSec()) * 100; //スイングの進捗割合
            //     let angle;
            //
            //                if (swingProgressRate < 50) angle = (swingAngle / 2) - ((swingAngle / 2) * ((swingProgressRate * 2) / 100));
            //                else if (swingProgressRate > 50) angle = -(swingAngle / 2) * (((swingProgressRate - 50) * 2) / 100);
            //                else if (angle == 0) angle = (swingAngle / 2);
            //                else angle = 0;
            //                if (((Math.floor(this.elapsedTime / this.oneBeatSec())) % 2) == 1) {
            //                    angle = angle * -1;
            //                }
            //                return Number(angle);
            //            },
            beatNow() {
                return (this.beatTime + this.oneMusicalBar) % this.oneMusicalBar;
            }
        },
        methods: {
            //一拍の秒数
            oneBeatSec() {
                const sec = (60 / this.bpm) * 1000; //ミリ秒
                return sec;
            },
            //beatが鳴った回数
            //beatTime(){
            //    return Math.floor(this.elapsedTime/this.oneBeatSec());  
            //},

            // swing(){
            //     const swingProgress = this.elapsedTime%this.oneBeatSec();              //スイングの進捗ミリ秒
            //     const swingProgressRate = (swingProgress/this.oneBeatSec())*100;    //スイングの進捗割合
            //     
            //     
            //     
            // },


            //再描画間隔
            // rerenderInterval() {
            //     const interval = 1000 / this.fps;
            //     return interval;
            // },
            //再帰的に使える用の関数
            // elapsedTimeCountUp() {
            //     if (this.isMetronomeRunning) {
            //         const interval = this.rerenderInterval();
            //         window.setTimeout(() => {
            //             this.elapsedTime = this.elapsedTime + interval;
            //             this.elapsedTimeCountUp();
            //         }, interval);
            //     }
            // },
            soundBeat() {
                let beatVolume = 0.5;
                if (this.oneMusicalBar != 0 && (this.beatTime % this.oneMusicalBar) == 0 && this.soundMusicalBar) {
                    beatVolume = 1;
                }
                source = audioContext.createBufferSource();
                audioContext.decodeAudioData(this.base64ToArrayBuffer(base64Text), function(buffer) {
                    source.buffer = buffer;
                    gainNode = audioContext.createGain();
                    source.connect(gainNode);
                    gainNode.connect(audioContext.destination);
                    gainNode.gain.value = beatVolume;
                    source.start(0);
                });
            },
            beatAngleCount() {
                this.beatAngle = this.beatAngle + (360 / this.oneMusicalBar);
            },
            beatLoop() {
                if (this.isMetronomeRunning) {
                    // this.beat.play();
                    this.beatTime++;
                    //this.elapsedTime = this.beatTime * this.oneBeatSec();
                    // this.elapsedTime = this.beatTime*this.oneBeatSec();
                    this.beatAngleCount();
                    this.soundBeat();

                    this.setLoop();
                }
            },
            setLoop() {
                const interval = this.oneBeatSec();
                this.beatTimer = window.setTimeout(() => {
                    this.beatLoop();
                }, interval);
            },
            runMetronome() { //メトロノームを始動させる
                if (!this.isMetronomeRunning) {
                    this.isMetronomeRunning = true;
                    this.setBeat();
                    this.beatLoop();
                }
            },
            stopMetronome() { //メトロノームを停止させる
                if (this.isMetronomeRunning) {
                    this.isMetronomeRunning = false;
                    clearInterval(this.beatTimer);
                    this.beatTime = 0;
                    this.beatAngle = 0;
                    // this.elapsedTime = 0;
                }
            },
            base64ToArrayBuffer(base64) { //base64をArrayBufferヘエンコードする関数
                var binary_string = window.atob(base64);
                var len = binary_string.length;
                var bytes = new Uint8Array(len);
                for (var i = 0; i < len; i++) {
                    bytes[i] = binary_string.charCodeAt(i);
                }
                return bytes.buffer;
            },
            setBeat() {
                audioContext = new AudioContext;
                base64Text = "SUQzBAAAAAAAI1RTU0UAAAAPAAADTGF2ZjU4LjY1LjEwMQAAAAAAAAAAAAAA//uQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAASW5mbwAAAA8AAAASAAAfBAAaGhoaGigoKCgoKDU1NTU1Q0NDQ0NDUFBQUFBeXl5eXl5ra2tra3l5eXl5eYaGhoaGlJSUlJSUoaGhoaGhr6+vr6+8vLy8vLzKysrKytfX19fX1+Xl5eXl8vLy8vLy//////8AAAAATGF2YzU4LjExAAAAAAAAAAAAAAAAJAaHAAAAAAAAHwTlfZ9QAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA//uQZAAAAztWqI0NAAIrAAWSoAwADyWZNBkaAAFvsGLnCqAAAL/ru8AxcCCE96kXF2DgBQFg0AQFg4sG4Nw/PQXPgUFEl7n/3fe//9P3d7h34Sv34Tl33e9ERNK///e4IFDJFDJLF3vS9EqXe5d30RMgUSnLFz8t9Er//csXeC3wEfDLABn//BGBgeAKjf/6jgnPg+fKAgUd/4PwfiAMFHA+UOBi/wfeD6jn///gg4Mf4Y/W/k1+OsUN4X0DTfDLwB88AYYGS+8BoGDbEBod/gZgMAFMEDgHBP/AaQCThcwA0UEf//iUAJABc5gIADl//5EyYKI4x7KhP///ijkQIOHvkAOjvJg0///8vmROEgfLhEC+6R3////8nyXNSJj2ak+SZ4vkmaG5ur////2iAAAAEAsoAAAAD3TyAB5F3PEwb/Pe3ioRDQCmBVEwAmIzeF4ITcJQrmMJf7NmGC0CAJAmjYe/1U8/cRRMPBgGwmCp/3Z/x0iWn///8/r9U//9GeUJPmF9HBsHH8LqgEAiEAgEAgEAgEAgEAHaHK8TUBs+//uSZAqABEpW2+42oAQ1qkjQwSgAENElT9zKAACJABnLgiAABlA8rVgYCAIGEQDKNQAgAA2KjwMKigaIYgYi4jExACBoGDgCGMgsnVejXDAIYQDZxEw5Bev8PTHWJQEFzcb1X/i4xtkVKxFjEmP/8eAtDH4oDNjmFUcZBP//3OHTEwMzrJpGP///pE0YnCkUygWCmYHDMwSMnn+o02II2m/+IMKYmCiIci8FL5VH1+JThcAtnD02ccv8VSIesaUOzn////////////hdfyttDmhGAAAAGgdEaEcB5HdWH4lmZ6HY7ByKGiQJQoQgglYZGDCIIRQXOhloBRSBhE4HDTgBowCgQAAQ3J0ZAuC3DtJoZAeA9c+OsjiBjnGpERtlkuj2Q0iBiRc+pFBNRIjqJtAyND39AzTQSWyKTalLV0NkjqB9RPl03SY439b0P//9E9//8uAJV//5Cz//6P/9JeGFu0f2eQl3kFbdysuf2rPstXEFZ+hN39RBAAAAY+ErvMAepUyuWkNchl4nphhpkgfSZhbfx9QlERCAmEQaXmhrZv/7kmQVggSNSVHjbBZAIqAHPQAjABNZHUGNMFyAwovllAGkeCDqZ6hmGqh4oURQpgRBBCk3tVud6DmpVkwqRPdYZQEAQCK4OwAWlxUDkAYcDle6YEcolVITQSgBCWmun5iz22+SqUNx75nK7Xpmrez5VTAOJxuVieZKrXimZlyUO5f/6FV+YT//8AgAAAREAyF1L/xanbMmun+l3f7uh3/0mKKDLGM//pNc279KHqgCAAAf5TKsjtN5SwU1nN+orgw95bDXE3EJKxIs9QOCmJKm4FnCmnYpGzuHs/G3ZGfJgL6YsQDRAMGLCFQSCiAsDd0HRC3rcmvoONNU/ALboQz01I2etqp5ijJrlBIoahukS8ZKv8mqocWonI+OE83SVeOjG0zOfDGixDhiSnSEK1gHTovk1kqEkk9MoT8s/Vv/9BRjDf//w0k8AAc0LREL0ABbu5ogQUBtuuo4Lk9N79QMfpCtsH4gOVh8gXHvB8HOsaXD/8HxB/E6W7QAAAAAFrlW8lzMuS7td2YmwJyrLVX6ghgcOpCIyqdCQCKhZgIWVTP/+5JkFIYkn0VP42wXIC+ECh4BIgYS4PU9p+2RALiPZ/gjCeABFJ4GCfgVG7H5v4OsYmG0REFI03IWAkBzJpEPAS5nfWaCALBn0Fy5uzu1Gaxt5pFqKOZjlaYW0+Ps2BwqLjFt5V9n7HXp6I3Vs9lXvjmafsOULpOFg4rlhxSNWpb6Z+djkw4v/8Ip///8SA8tAEAAAAAh1zS0VkIKWstnfBjlAwRhRzCXKJHQ7wjtOhHpV0b5OOEFKV+3/FP9itdYAx9W5TGCNxLFEYJKlarj9T5YGY5H5UEGA0gAe7BhoCYYCGmjwhOjL7w14NNpbDgUIwxOMlBCQPRmAQQWXemLKPF2lbertcx44aXlOP7HmEtAdlrxdLTaof149uLkAfAOjyDGNfD8DaVmq36OmJwZbNveimZXTFWcIDQHysFpUP0J7zArxTMzb51gHWfLOfPzn8QfxKCwqEAAAQh1EmcpVvob51U7QiC05dz9eVvelV5xK0wxl8MKqU8ed6z1v9P+V/6aCHchEAAAAAAKP+feKavdbhOSSkfSRQFGJG1WQPdE//uSZA8CBGFKT3tmPxAzQxn/BSN2EYkvOa2gXIDLnql0IJta761E4TFh4FFpkcaYErGcwoBWxkwAwc8A0IIDi6DAm0nysCZDFlZE95hzGtP9NvVPyWO0cWqOM3Wdu5dvYQG4c/D1AuRqCPpvapMNIvUvW0z1OS1xQA4ETwLDUqXU5JQs/2NT//18eqNgRJ/iEzeAPAIQAAAAAhYe7eSagD9RZ1TRCUNguaLyzG0kqjAlQn8Wwrr5dn5rX2dXV7v/2Vs/QfJi71kgAAQf+NOyWWuo2jhR2JOXBT/wp0ZdA7Q1lqGrtRYMFIDPxIzkyJlEzAtMpwjR3sxAtEgUOEVJuksCmKwVpTD1V1LVNIzGI3UdaigJ+qWO0tNRV4GlFNamZBeXa/NUoP051ZT8linGnichPvWo6aoLGKHUQNCLQPYqbH/+lv/8xuhjERhQ36goHFgDaMgABFM2D8wCJDPdL+iXYZSKYz303Yj1RhyiDUr/1O1//Nf/r8v/9OBZou/5vVo/ptoTXYAAAAAA0f+G3Ke51oCf5eVt+r8CNjcRucSi0P/7kmQOh5RcSM1rRhcgMwL5mABMOBDg4y6NmZxIxpolgBeWEhoyqoK1kA5IcnQnhTmHVGE7HsTk48arBxplRccwwKMsxUyoHTXqq19VSRiUMqdFp0Mw7AsDwlk0Nz/LFDtpUdpm6PCNClka+ZT8vd4pkWf/XivhrujaDUmlSR51dW5jZ/R27+l+iM9CigEBRj/5EA9AAko6UdE4SdF8dCUO6ojgzAie+oTG7S6FbE7H3uLweAoDMC0kRAQWKunP9e2rFj1WsB2j//cml7a226UcobK4ER7BrkMBZRAyHFBQHCacw4BgEPMtNDef08EEAyobU6GZl61igpLu0osAOEpm0BuT6MoQmpvpNN1daBm5MaXTaqw+47hS6QwFTw2zKEQRKHWCjhyZqtmdLeqN0MSzf3pyt24uvdcQhfC8u0/69Vj0T5Cynd6v+gAn3//NchHFoaSuHMS1mOeqteqWL4VIkDIYSFY0CoZQFHI69q2Yv5f/l/f95hb8qYp0/XTVDlZQAACTOpUxu8HoHhPbGgDwNnkgHDFsatyljaJDiwKDj4z/+5JkEYJEDzlL2ZtjwDSEGWwF4ngQKOMpDeE0wLsQpNQWFeCeNNsMjRhg368MMWyI5BQYSAKRbBUwoUwdksNo1sUfqXN4Pw5E7h7C4dnj0ZHROH90xIJ8dEwoj6vEnLTrUDT32mLr3S0+Pb/fa703rDdisJdPH3aZrX7E5YGMuq/eLgACMAAAAw/83ZRKHPnMdhV7k/YxUG4rmO8Fz3ZUP7qzW/R168hKYfDB3Kd91H6q2fzlH7Fg2gAD//8ORzGLPZTtJdmrC7rlKYvcoYxZHoIAxEAGalphMEYJAmWHpj2ufE9pAnR4NAhqPGiLBAytDFS1jS1N2gq4hbfMfpYFaRDD9P45jaw64sObjMy2JibcX5Z0EZs3bXnLrmYyOmbxn5DZ/3sKrMC4j4CsJ5FC+bNBqHbxmj9AJAf+dLMuSntlSYtYKR2PQSLROsIRamzRlcxptZ/Vbizm2eoFY6GhK/kxF6vv/WoBSJgAAJMzreOGaUKyCkEIzVk4kmRkAUoAuuUQgIVJThl8yV4NBDTpXICkwOVzIBNtygHZuw1lbnQP//uSZB0D88I2ytmbTEAyxrjAHYeEDxzbJIZtD4jUkCPAAL1ISwarBHXCVy9Vxr0k5GI272S8BdVVlCCSYbAsA1EY+ignlbf+bv7rY2Pr/fca2W0+UR+38jWYBSNwiWtzU/sm/85PTgcSHXyQ6DRQdkAAAfL7hmXVBoQrPfnzVBUBTUM6nvjYsh///jnRmHP9Rmo9iGkApRAATM7zLTp8JYpGILoYNISImj0sTSVuEAADDsyhSBTGasHDtGeJCFBWasGs0eERgyVwyEo9LmRSh5OtIly4bnI3LZHGHif5/XTZM7r7w/Admkk9Kv164suqyLK0e9/qsDBDNFri/rTlOCnEgKgeAUPE+axcBJb6/2q/hibhwl+F4qQtYhBigLTGaryjcfl/g82Smt03red2lcoKh98P/9SlnBoRfCKvfdEaPU8GVAAA/5OrMJVD4QSJMB0SxcaVo66TLUwjAQUysoNNZAuCmP8QiLBgXMSHDAzAaJ5ADgZNxQZ3HMnICeeVpPL1jEBPo7TpMtn2xO5Bj/x2zDdaNU0qYW8KnEcJdQ1NOP/7kmQvg9PCN8jBe0viNUPJBgXpgg7I4R8E6S+AuZAjgAehmGf/pSaMm4ev8utqcJV0klQ2KkGsZsm+PG7AgAQAf/5HzCM5WE7VGH6pkgLcxgjijQiDPlqbNx+xXtVpU6hnf9NDhUNMT9qEf0B4OODYsUAGAD/1HJ9ABApAzEYGYEIJzr8q+fh0yoRNofMoONIrOujAU80W0bIFAAcClYFdBbEtizBvXRkK9HhWFeWBYLch1pmA5lpLWWKXKs/g6E+6O2SwpukkDBEgxa7ysSZbcsghN8YR3ZV574VFCQGVyRllER5JstWy3+ZY8TplAC1jVJyRTASUoFFmEkh+7c6W0k1DjA+GpH9DIU0cvp5KgSfKoLzhVFUWAAf//9+lp7U5DUajLTow/MsfyMyVbKJ6nQMAAGAm8Oxng0c0znYl52sAY0NnIgmYI4piCkIbJVs4jw0OC84vlBaw0nn2Zw9PWqKPkpdYgH5dOy2US+PgwH9Dqu3Hdv2PO3ghdXf8zM5Pdu12LYDMRVrr6W3KOgxL+Y6coQxVeipzISgdJr3l42L/+5JkRQPzxzjGq3hlIDHmqKAFgnZNqOEbBOjNwMcbogAgIlCxstdn0fZV4MT0ZjXoYEKU6tl+/nFJGsqr9PlKUQ4qL+gAWA//qtWLhQNnlQShAEqd6Iv/HV20Jcw3wY2Yk85w0mE9mcwSssFTEoX0TrYeLAVqu4yu8+jPWuyKSQ/J4nONQm6KzOu+4lHT3puZrw1Mo/SCeOIjXaLbvWpYHT2G75/n2P8d9KWPJycVOWjo9VOryKil+YK3//DA7GGDxSy3SrMEZ+GmBfvV4lbhuLSvuh36X91t8lXdKf///6v//6EDAAeDRwZjDmnXbnWxxGMzTjOIpmEBphx4MqBhYQZsiGuEpoRseWNmjAZIMOmWuLwKHK2Q0NBL+iABaU8dlR9rLLnnkzqrubd/X8eNXqpOysKdQxCDTHewq94zvtQt0ngak+ntcK7eJr49v81+/22FBU5NXrC1vX7UCfabCJ2E+f7RtJiQRk8zup7e30JqV0AkM4lwwopdtEcz6rQEYiuzBVX5E5c7QcVRD/8prRq0SQx3dRrNSmhxnDOXgFRg//uSZF8K8883RahbevIuZsiQBSKCDYzhFqNtLYDTj6JAF5nY1EHCAQdETIjU89dNjLRwdQTEQ2hze5FRLVOp3XnQlJnQOmY+cWaFFX3kcghmGGxwMwPEoUAMouJAOXG2U27jr5bHNiznIkU4+7lH///U3JKmXudnSdSvwxNInAm94xCVh6E0L0Z5bQjoaZP245Dg1Nck9sL0hrKORy6w72nrJLpxrFFVsY3W/6TFAiX6TWOGgOxMQC07xrsFNwd5uy5xksBWYZsZm5CxhqabrWGtkxmqYBmYEBACDCqBIVtJi951GIrmYc2F83yiUBN/L3ccrKMSulhUOdhujgSusdYCXRQEw+g3+1uTnXhq6iP//2KPEMFxiByLMHJMHX6v4xCACAB8v1Rp+BHnLesiZDnLAW8R45jSKN3Dlmldzb3BrvQQLd81nV6OUhyUa9rtZW///6yTiK/oyuVIkS4Qg9f6q/UP8glYVwU6DLpA+SZQ6YckewUcRyIlJko5mgjlNPWCSLf16nwemUsOpGLv2wWH3Llc3UkcFS2s1l98IzMWIP/7kmR5D7OUOMSA+0NwNUaotQXieAzE0xID6K3A2pAiQBeZ2HZTCGpyAYHCvoYzoYQYvfr+nQPHAUCiL///29n5+ZkhC+135bQ+AwBKgJZylOBMiiE1JbqaszB6+6J9f3cbtVtNzdQY00We9Y3Mf+zK//////6VP9A9A+Uy0LjA7PxBUa4Ura0yR0k3xDFOdANGoMlxAeU3ZUgJkJUtNVL4sFXowmZWK7VGtFuDd3Bf1wog+DB4y+FR/aWAoG+HtRyltqDq4dW/um+sotBNpiiMbYiR1vJq9smZlepnkdVK93qJe5DOop/po4y3RlMtESGMGkZUcLUIFVJJqpqS9dyM27M4q68187on8RXfLf6/R1f///q8QBmnmqKkoqfdNQV5XDsfuw01hO8Krr+CuBjLGA+A3yy4OmGglD4pPuMUYywcaefd45KrJAu4rJqErKy5pacUKQIkFv3uq6FJCd3R/36ak7IbmX6rwnsTc5u3zq84hPtkTSAAAkcckr2U5qgFr6RdEoTrvvSv5Rxo3/9v/o/s47/39PR++nUtKiEAAAD/+5JklYYDgyPDACzQ0C3j+IAJ5oIL3IsRIOWJgKUII7QQjOD5991QPQdrJCq8ctP83VdIiAF/kyxExE25liQp2ES8MlmaDko+RMCYipTsg4tKqN81mE8ECpUkaStemmh5+pxVHMvxYdI75wQlmajqcpLiKBAFgIWCyS17tGWZ9bEi9S1NJv7NFX77urQ2xC7MjfgAAA81RXQJPMpJjuoAZLY+z4SkZ1NYt0TgE+wmpJ1SVfVm1lPHuYryVP7/X1M7dH7/jKsu8AtSqkWoNnUYAA9SKVSmXww6jap/PeCmwuXMquPuKMUvMYML0u02R0JVFRSswLC8Oxj5YuneRx/52fGSpOmTcuujJYvSxTrXgALsHF7uPvNKdIJ/1iRlsey5CGq3+vrbUsmRo26bQEAAJpjnWJEeo+JB+ypdrx1k2Rhjj4RKuRgytMJXNcn1Hywbv/9ie7R7KHe19//1fZ36kU4ACgAAAjElylJscMHVrW4erTWqlI6PFHw4YZ9WcjUXgDShoEJzO5JPz/TA2igxUl5PJNRy2PbPRjXlSKSIiPWV//uSZMEGA40hw0Dae0A1QhipBO+ADLSLDwHpjQDRiGKkF5oIkAIEAiTHmXv7A8cYB1LYdI5V9rnBxxt8VwVa70/aZUs1sal7GVfwqRzo5EN7uO0nImsdOqGEBotXIDN3f/pPE53Vq9aUdiVLV9BXU3v/Ro7PpZ//////UowAAAESrIhiqccRjgfsWrv9DrVcFZi6TGjGETTKgOIMGbN4WNJEM0aO6gEnZAGdCIRJecpsuNCleS+eZw+i6p+IwqliMHtZqUjuSnk/EaZhMjeJ/9HkfwJuEN3CXF1UBLVHtbIwQjEAxfG3sb2e/IdI3kRiNMUEKFvhtccck3AMI1hGwbOg1p2qXuoUkiu5YvMc04aMJ7CGpzIHKJ7+HflGfep8JbHwfxLERCKY3PF85pmec1r8yz32tO89IskIFH32o9DbFVTPV/X9TLdFC/2eiruuvqrV/HXctY81l/y6vyrBDUYKXVLS0zkkAStMwg4Nvbzc4AgojPB4ijC1yYDBFZ4W5KuGDNZYX77p6YnSc+JRITnQnhKXhxjMeJrB0XBAHonL+v/7kmTfgPM1IUVoeErQMgOYYAXmghOljwUkaG3I5g1hgAEwmNmZ/Ujdltxf1nUTnVrW+Z8/vGZ8MtO3ZrMjpk1U+sDmD1Ieni1B8OGv4Q9a+lWar8mfCVulIUpyDrqCSbpdx4JyGxrX+QtQ0ApUyJqeIuANIrFcN8/7oHUHwUUyW1/9ktNii2gC/3kSsQNt6F+/tfcfZD065/RV//7fL60BETuRqgGv6ifwdBrZEBSkBkVNtEzDSoAVoNQTDyAyYUaomKoE2CgdJg06+kMwS5EhjAlGZOJMZ95O5ccnT+nVlq0SARMo86Z2Z6l5Zu/c5YigMtcp3bIhsQIWC72sgZbwGEEgcSuAQtfGxV9fPqxjj5gGwSOhwEQzcUcguS/y/xn284yUGisDrPc0UIcoryXVnp1Y0p6xozBMhFGs6hy2u7Pf/7O6x5r//3f113IsSiEKxRQgQkJm7zvLxctwWDVgSQAM0zaM80oz6s3mI1BRJsgfmXIS5isMJeuzppbKpYuZpzDVcOtAMhvyKORKCaz7P1DVWW0sexa0y6PdeJOkiHL/+5Jk5ofUkF7Ag2weMjljWGAF6IIPiKkEoO2NUMQN4cQXjhCIUPw8CUpgnFYBoPNHxTKvC43MGTBS1FXdR3NX3NRc1rVjhcwiylF1IWBpps0SphtcUyTBXJEujjIKh4qfKZGy0MpnqGPmKuRmWXdjDLciAdYtBRAAApLYlSd66K4lDAEFqA1WrueO8c+EoTYCSiowCtYVMwL14sBRq7qlCL/WGDX+1CMfX7cjlN1Wi2RQur/jM37RgICoDm+rOWcstUORPN7MHqmlieB4BeOd4MeCwZel/YdhqXU1C/ti1NOVMP1TU2WPKaNWVAQESoUBE1UAmZvjHhgKo0AjUTAwEKL/9tVL/jFDUv///9jntVK7VYzNsbHSZlL8/i51VUvyh/z+rGb4xqv8ZqoCJiVg78Y3IgCuuGQoci5yyFNCSgpI5ZpFFqS0lnbvz36i3tLf9U8Vt/1uU8RP/9WHeJfK1UxBTUUzLjEwMFVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVV//uSZOkPRS9pPwB6Q3I9YkhoBQxyD9Gc9gHkb0i5iSDwEJjgVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVTmvAkxBTUUzLjEwMFVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVf/7kmRCD/AQAIADIAAIAQAQAAgAAQAAAaQAAAAgAAA0gAAABFVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVU=";
            },
        },
        watch: {
            oneMusicalBar: function() {
                this.beatAngle = ((this.beatTime + this.oneMusicalBar) % this.oneMusicalBar) * (360 / this.oneMusicalBar);
            },
            bpm: function() {
                clearInterval(this.beatTimer);
                if (!this.bpm == 0) this.setLoop();
            }
        }
    };
</script>

<style lang="scss" scoped>
    .metronome-wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 20px;

        .musicalbar-view {
            display: flex;
            justify-content: space-around;
            position: relative;

            .one-beat {
                border-radius: 100%;
                height: 10px;
                width: 10px;
                background: red;
            }

            .one-beat2 {
                border-radius: 100%;
                height: 10px;
                width: 10px;
                background: red;
                position: absolute;

            }

            .beat-now {
                background: black;
            }
        }

        .hand-wrapper {
            height: 200px;
            position: relative;

            .hand {
                position: absolute;
                left: 150px;
                top: 20px;
                background: red;
            }
        }

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
                top: 90px;
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