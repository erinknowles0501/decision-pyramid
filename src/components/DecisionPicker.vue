<template>
    <div
        class="picker"
        :style="`background-color: hsl(${color}, 65%, 70%); grid-row: ${option2.id}; grid-column: ${option1.id}`"
    >
        <div v-if="!localValue" class="picker-area">
            <button @click="select(option1)">
                <span>{{ option1.id }}</span>
            </button>
            <button @click="select(option2)">
                <span>{{ option2.id }}</span>
            </button>
        </div>
        <div
            v-else
            @click="localValue = originalValue"
            style="height: 100%; width: 100%"
        >
            {{ localValue.text }}
        </div>
    </div>
</template>

<script>
export default {
    name: "decision-picker",
    props: ["value", "option1", "option2"],
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

.picker-area button:hover {
    background: rgba(255, 255, 255, 0.3);
}

.picker-area button:first-child {
    clip-path: polygon(0% 0%, 0% 100%, 100% 0%);
}

.picker-area button:first-child span {
    position: absolute;
    top: 1em;
    left: 1em;
}

.picker-area button:last-child {
    clip-path: polygon(0% 100%, 100% 100%, 100% 0%);
}

.picker-area button:last-child span {
    position: absolute;
    bottom: 1em;
    right: 1em;
}
</style>