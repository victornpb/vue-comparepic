<script>
export default {
    props: {
        labelA: String,
        srcA: String,
        altA: String,

        labelB: String,
        srcB: String,
        altB: String,

        value: {
            type: Number,
            default: 50,
        },

        divider: {
            type: Boolean,
            default: true,
        },
        handle: {
            type: Boolean,
            default: true,
        },

        hover: {
            type: Boolean,
            default: false,
        },

        blend: {
            type: String,
            default: 'normal',
        },
    },
    data() {
        return {
            current: this.value,
            width: 0,
        };
    },
    mounted() {},
    computed: {
        percentA() {
            return `${this.current}%`;
        },
        percentB() {
            return `${100-this.current}%`;
        },
        opacity() {
            return 1 - (this.current/100);
        },
    },
    methods: {
        setImgWidth() {
            this.width = getComputedStyle(this.$el).width;
        },
        pointerMove(e) {
            if (this.hover) {
                const rect = e.target.getBoundingClientRect();
                const x = e.clientX - rect.left; // x position within the element.
                this.current = (x / rect.width) * 100;
            }
        },
    },
};
</script>
<template>
    <div
        class="compare"
        @mousemove.passive="pointerMove"
        @touchmove.passive="pointerMove"
        v-resize="setImgWidth"
    >
        <!-- Pic-B -->
        <img :src="srcB" :alt="altB" @load="setImgWidth" />
        <div v-if="labelB" class="label" style="right: 1.5rem;">{{ labelB }} <small>({{percentA}})</small></div>

        <!-- Pic-A -->
        <div class="reveal" :style="{ opacity: opacity, 'mix-blend-mode': blend }">
            <img :src="srcA" :alt="altA" :style="{ width: width }" />
            <div v-if="labelA" class="label" style="left: 1.5rem;">{{ labelA }} <small>({{percentB}})</small></div>
        </div>

        <input
            v-model="current"
            type="range"
            aria-label="Compare ratio"
            aria-valuemin="0"
            aria-valuemax="100"
            :aria-valuenow="current"
            min="0"
            max="100"
        />

        <div v-if="divider" class="divider" :style="{ left: percentA }"></div>
        <div v-if="handle" class="handle" :style="{ left: percentA }">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 246 246" width="32" height="32">
                <path
                    d="M187 173l59-50-59-50v37h-38v26h38v37zM59 136h38v-26H59V73L0 123l59 50v-37z"
                />
            </svg>
        </div>
    </div>
</template>
<style lang="scss" scoped>
.compare {
    position: relative;

    display: inline-block;
    overflow: hidden;

    user-select: none;
}
.compare > img {
    max-width: 100%;
    height: auto;
}

.reveal {
    position: absolute;
    z-index: 1;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;

    overflow: hidden;
}
.reveal img {
    width: 100%;
    max-width: none;
    height: auto;
}

input[type='range'] {
    position: absolute;
    z-index: 2;
    top: 0;
    bottom: 0;
    left: 0;

    width: 100%;
    height: 100%;
    margin: 0;

    // cursor: grab;

    opacity: 0;

    -webkit-appearance: slider-horizontal !important;
    -moz-appearance: none;
    -ms-touch-action: auto;
    touch-action: auto;
}
input[type='range']::-webkit-slider-thumb {
    height: 300vh;

    -webkit-appearance: none;
}
input[type='range']::-moz-range-thumb {
    height: 300vh;

    -webkit-appearance: none;
}
input[type='range']::-ms-tooltip {
    display: none;
}

.divider {
    position: absolute;
    z-index: 2;
    top: 0;
    bottom: 0;

    width: 5px;

    transition: opacity 0.5s;
    transform: translateX(-50%);
    pointer-events: none;

    opacity: 0.3;
    background: #888;
}

.handle {
    position: absolute;
    z-index: 3;
    top: 50%;
    left: 50%;

    width: 44px;
    height: 44px;
    padding: 6px;

    transition: background 0.3s, box-shadow 0.3s, opacity 0.3s, transform 0.3s;
    transform: translate(-50%, -50%);
    pointer-events: none;

    color: #000;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.5);
    box-shadow: 0 0 12px rgba(0, 0, 0, 0.4);
}
.handle svg {
    opacity: 0.7;
}

input[type='range']:active ~ .handle {
    background: rgba(255, 255, 255, 0.85);
    box-shadow: 0 0 3px rgba(0, 0, 0, 0.4);
    transform: translate(-50%, -50%) scale(0.6);
}

input[type='range']:active ~ .divider {
    opacity: 0.1;
    width: 1px;
}

.label {
    line-height: 1;

    position: absolute;
    top: 1.5rem;

    padding: 0.5rem;

    color: #fff;
    border-radius: 4px;
    background: rgba(0, 0, 0, 0.33);
}
</style>
