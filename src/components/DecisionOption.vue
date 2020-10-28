<template>
    <div
        class="option"
        :style="`background-color: hsl(${color.hue}, ${color.saturation}%, ${color.lightness}%); grid-row: ${id}; grid-column: ${id}`"
    >
        <div class="display-letter">{{ displayLetter }}</div>
        <div
            @click="settingOption = true"
            v-if="!settingOption"
            class="display-option"
        >
            {{ localOption ? localOption : "Click here to set an option" }}
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
// TODO: Generate options (in App.vue) with ids as letters, instead of translating them here.
const letters = ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L"];

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
        displayLetter() {
            return letters[this.id - 1];
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
    z-index: 20;
    color: rgba(255, 255, 255, 0.3);
    font-size: 75px;
    padding: 0.1em;
}

.display-option {
    cursor: default;
    height: 100%;
    width: 100%;
    box-sizing: border-box;
    overflow: hidden;
    text-overflow: ellipsis;
    padding: 0.5em;
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
}
</style>