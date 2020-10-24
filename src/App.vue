<template>
    <div id="app">
        <DecisionOption
            v-for="(option, index) in options"
            :key="index"
            v-model="options[index]"
        />
        <DecisionPicker />
    </div>
</template>

<script>
import DecisionOption from "./components/DecisionOption.vue";
import DecisionPicker from "./components/DecisionPicker.vue";

export default {
    name: "App",
    components: {
        DecisionOption,
        DecisionPicker,
    },
    data() {
        return {
            options: ["", "", "", "", ""],
        };
    },
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
