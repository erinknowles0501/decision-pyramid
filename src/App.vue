<template>
    <div id="app">
        <DecisionOption
            v-for="(option, index) in options"
            :key="`option-${index}`"
            v-model="options[index]"
        />
        <DecisionPicker
            v-for="decision in getPickersNumber"
            :key="`decision-${decision}`"
        />
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
    computed: {
        getPickersNumber() {
            let count = 0;
            var i;
            for (i = 1; i < this.options.length; i++) {
                count += this.options.length - i;
            }
            console.log("count", count);
            return count;
        },
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
