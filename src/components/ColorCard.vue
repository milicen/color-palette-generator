<script>
import {computed} from 'vue'
import {toClipboard} from '@soerenmartius/vue3-clipboard'

export default {
    props: {
        color: Array
    },
    setup(props, {expose}) {
        let dict = {
            0: 0,
            1: 1,
            2: 2,
            3: 3,
            4: 4,
            5: 5,
            6: 6,
            7: 7,
            8: 8,
            9: 9,
            10: "A",
            11: "B",
            12: "C",
            13: "D",
            14: "E",
            15: "F"
        }
        let hexValue = computed(() => {
            return `#${destructColor(props.color[0])}${destructColor(props.color[1])}${destructColor(props.color[2])}`
        })

        function destructColor(color) {
            let hex_1 = parseInt(color/16)
            let hex_2 = parseInt((color/16 - hex_1) * 16)

            console.log(color % 16)

            return dict[hex_1].toString() + dict[hex_2].toString() 
        }

        expose({hexValue})

        return {toClipboard, hexValue}
    }
}
</script>

<template>
    <article class="card" @click="toClipboard(hexValue)">
        <div :style="{'background-color': hexValue}" class="swatch"></div>
        <p>{{hexValue}}</p>
    </article>
</template>

<style lang="scss" scoped>
.card {
    position: relative;
    top: 0;
    width: 14rem;
    padding: .5rem;
    border-radius: .5rem;
    text-align: center;
    overflow: hidden;
    background-color: white;
    box-shadow: 0 .2rem 10px 2px rgba(0, 0, 0, 0.096);
    transition: all .3s;

    &:hover {
        box-shadow: 0 .35rem 10px 2px rgba(0, 0, 0, 0.233);
        top: -.2rem;
    }

    .swatch {
        width: 100%;
        aspect-ratio: 1;
        border-radius: .5rem;
        background-color: aquamarine;
        transition: background-color .5s;
    } 

    p {
        margin-top: .5rem;
    }
}
</style>
