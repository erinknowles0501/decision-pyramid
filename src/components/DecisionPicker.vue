<template>
    <div
        class="picker"
        :style="`background-color: hsl(${color}, ${isHighlighted ? 90 : 65}%, ${
            isHighlighted ? 85 : 75
        }%); grid-row: ${option2.id}; grid-column: ${option1.id}`"
    >
        <div v-if="!localValue" class="picker-area">
            <!-- TODO: Could refactor this to be v-for="option in [option1, option2]" -->
            <button
                @click="select(option1)"
                @mouseover="
                    $emit('highlight', {
                        option: 1,
                        id: option1.id,
                        other: option2.id,
                    })
                "
                @mouseleave="$emit('highlight', { option: null, id: null })"
            >
                <span :style="`color: hsl(${color}, 100%, 20%)`">{{
                    option1.letter
                }}</span>
            </button>
            <button
                @click="select(option2)"
                @mouseover="
                    $emit('highlight', {
                        option: 2,
                        id: option2.id,
                        other: option1.id,
                    })
                "
                @mouseleave="$emit('highlight', { option: null, id: null })"
            >
                <span :style="`color: hsl(${color}, 100%, 20%)`">{{
                    option2.letter
                }}</span>
            </button>
        </div>
        <div
            v-else
            @click="localValue = originalValue"
            class="picker-selected"
            :style="`color: hsla(${color}, 100%, 20%, 0.5)`"
        >
            {{ localValue.text }}
        </div>
    </div>
</template>

<script>
export default {
    name: "decision-picker",
    props: ["value", "option1", "option2", "isHighlighted"],
    data() {
        return {
            originalValue: null,
        };
    },
    created() {
        // use the JSON functions to remove reference to this.value (otherwise will update when this.value does!)
        this.originalValue = JSON.parse(JSON.stringify(this.value));
    },
    methods: {
        select(option) {
            this.$emit("input", option);
        },
    },
    computed: {
        color() {
            return (this.option1.hsl.hue + this.option2.hsl.hue) / 2;
        },
        localValue: {
            get() {
                return this.value;
            },
            set(val) {
                this.$emit("input", val);
            },
        },
    },
};
</script>

<style scoped>
.picker {
    padding: 0;
}

.picker-area {
    height: 100%;
    width: 100%;
    position: relative;
    box-sizing: border-box;
    padding: 0;
}

.picker-area button {
    padding: 0;
    box-sizing: border-box;
    position: absolute;
    height: 100%;
    width: 100%;
    border: 0;
    background: transparent;
}

.picker-area button:active {
    outline: none;
}

.picker-area button:hover {
    background: rgba(255, 255, 255, 0.3);
}

.picker-area button:first-child {
    clip-path: polygon(0% 0%, 0% 100%, 100% 0%);
}

.picker-area button:last-child {
    clip-path: polygon(0% 100%, 100% 100%, 100% 0%);
}

.picker-area button span {
    position: absolute;
    opacity: 0.6;
    font-weight: 600;
    font-size: 1.4em;
    font-family: "Trispace";
}

.picker-area button:first-child span {
    top: 1em;
    left: 1em;
}

.picker-area button:last-child span {
    bottom: 1em;
    right: 1em;
}

.picker-selected {
    height: 100%;
    width: 100%;
    padding: 0.5em;
    box-sizing: border-box;
    cursor: default;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    font-size: 0.9em;
}

.picker-selected:hover {
    background: rgba(255, 255, 255, 0.3);
}
</style>