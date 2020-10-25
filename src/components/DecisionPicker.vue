<template>
    <div
        class="picker"
        :style="`grid-row: ${option2.id}; grid-column: ${option1.id}`"
    >
        <div v-if="!localValue">
            <button @click="select(option1)">
                {{ option1.text }}
            </button>
            <b>vs</b>
            <button @click="select(option2)">
                {{ option2.text }}
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
    background-color: green;
}
</style>