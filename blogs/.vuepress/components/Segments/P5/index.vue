<template>
    <div class="p5-con"></div>
</template>

<script>
function distinct(arr) {
    return Array.from(new Set(arr))
}

const initialEvents = [
    'preload',
    'setup',
    'draw',

    'deviceMoved',
    'deviceTurned',
    'deviceShaken',

    'keyPressed',
    'keyReleased',
    'keyTyped',

    'mouseMoved',
    'mouseDragged',
    'mousePressed',
    'mouseReleased',
    'mouseClicked',
    'doubleClicked',
    'mouseWheel',

    'touchStarted',
    'touchMoved',
    'touchEnded',

    'windowResized'
]

/** @see https://github.com/Kinrany/vue-p5 */
export default {
    name: 'VueP5',
    props: ['additionalEvents'],
    computed: {
        p5Events() {
            const { additionalEvents } = this
            return Array.isArray(additionalEvents) ? distinct(initialEvents.concat(additionalEvents)) : initialEvents
        }
    },
    mounted() {
        this.initP5()
    },
    methods: {
        async initP5() {
            await this.$utils.loadScriptFromURL('https://cdn.jsdelivr.net/npm/p5@1.0.0/lib/p5.min.js')
            new p5(sketch => {
                this.$emit('sketch', sketch)

                for (const p5EventName of this.p5Events) {
                    const vueEventName = p5EventName.toLowerCase()
                    const savedCallback = sketch[p5EventName]

                    sketch[p5EventName] = (...args) => {
                        if (savedCallback) {
                            savedCallback(sketch, ...args)
                        }
                        this.$emit(vueEventName, sketch, ...args)
                    }
                }
            }, this.$el)
        }
    }
}
</script>
