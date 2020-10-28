<template>
    <div
        class="option"
        :style="`background-color: hsl(${color.hue}, ${color.saturation}%, ${color.lightness}%); grid-row: ${id}; grid-column: ${id}`"
    >
        <div class="display-letter">{{ letter }}</div>
        <div
            @click="settingOption = true"
            v-if="!settingOption"
            class="display-option"
            :style="`color: hsl(${color.hue}, 100%, 15%)`"
        >
            <span>{{
                localOption ? localOption : "Click here to set an option"
            }}</span>
        </div>
        <div v-if="settingOption" style="height: 100%">
            <textarea
                v-model="localOption"
                @blur="settingOption = false"
                @keydown.enter="settingOption = false"
                ref="input"
                class="set-option"
            />
        </div>
    </div>
</template>

<script>
export default {
    name: "decision-option",
    props: {
        value: {
            type: String,
            required: true,
        },
        id: Number,
        hsl: {
            type: Object,
            default: () => ({
                hue: 0,
                saturation: 0,
                lightness: 0,
            }),
        },
        letter: String,
        isHighlighted: { type: Boolean, default: false },
    },
    data() {
        return {
            settingOption: false,
        };
    },
    computed: {
        localOption: {
            get() {
                return this.value;
            },
            set(val) {
                this.$emit("input", val);
            },
        },
        color() {
            return this.isHighlighted
                ? { ...this.hsl, saturation: 80, lightness: 80 }
                : this.hsl;
        },
    },
    watch: {
        settingOption(val) {
            if (val === true) {
                this.$nextTick(() => {
                    this.$refs.input.focus();
                    this.$refs.input.select();
                });
            }
        },
    },
};
</script>

<style scoped>
.option {
    position: relative;
    box-sizing: border-box;
    padding: 0;
}

.display-letter {
    pointer-events: none;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    z-index: 10;
    color: rgba(255, 255, 255, 0.3);
    font-size: 75px;
    padding: 0.1em;
    font-family: "Trispace";
    font-weight: 600;
}

.display-option {
    position: relative;
    cursor: default;
    height: 100%;
    width: 100%;
    box-sizing: border-box;
    overflow: hidden;
    text-overflow: ellipsis;
    padding: 0.5em;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    font-size: 1.1em;
    font-weight: 400;
}

.display-option span {
    position: absolute;
    z-index: 20;
}

.display-option:hover {
    background: rgba(255, 255, 255, 0.2);
}

.set-option {
    height: 100%;
    width: 100%;
    resize: none;
    box-sizing: border-box;
    border: 0;
    background: rgba(255, 255, 255, 0.4);
    padding: 0.5em;
    font-size: 1.1em;
}
</style>