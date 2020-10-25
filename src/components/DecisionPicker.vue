<template>
    <div
        class="picker"
        :style="`grid-row: ${option2.id}; grid-column: ${option1.id}`"
    >
        <button @click="select(option1)">
            {{ option1.text }}
        </button>
        <b>vs</b>
        <button @click="select(option2)">
            {{ option2.text }}
        </button>
    </div>
</template>

<script>
export default {
    name: "decision-picker",
    props: ["value", "option1", "option2"],
    methods: {
        select(option) {
            this.$emit("input", option);
        },
    },
};
</script>

<style scoped>
.picker {
    background-color: green;
}
</style>