<template>
    <div>{{ option1.text }} + {{ option2.text }}</div>
</template>

<script>
export default {
    name: "decision-picker",
    props: ["value", "option1", "option2"],
};
</script>