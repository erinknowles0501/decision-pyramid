<template>
    <div>
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