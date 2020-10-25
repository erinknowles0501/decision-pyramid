<template>
    <div id="app">
        <DecisionOption
            v-for="option in options"
            :key="`option-${option.id}`"
            v-model="option.text"
        />
        <DecisionPicker
            v-for="decision in getDecisions"
            :key="`decision-${decision.option1.id}-${decision.option2.id}`"
            v-model="decision.decision"
            :option1="decision.option1"
            :option2="decision.option2"
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
            options: [
                { text: "", id: 1 },
                { text: "", id: 2 },
                { text: "", id: 3 },
                { text: "", id: 4 },
                { text: "", id: 5 },
            ],
            decisions: [],
        };
    },
    computed: {
        getPickersNumber() {
            let count = 0;
            var i;
            for (i = 1; i < this.options.length; i++) {
                count += this.options.length - i;
            }
            return count;
        },
        getDecisions() {
            // for Vue reactivity, push to this temp array, and set this.decisions to this at the end.
            let tmpDecisions = [];

            // for every option there is,
            // create a decision object that compares that option
            // against every other option.
            // prevent duplicates (ie entries for option1: A, option2: B AND option1: B, option2: A.)

            // start at first option
            // create decision object for option+[every option between currentOption+1 and optoins.length-1]
            // loop again at second option+[every option between currentOption+1 and options.length-1]
            // keep doing this until second-last option (last option will have no matches)
            this.options.forEach((option) => {
                let currentOptionI = this.options.indexOf(option);
                console.log("currentOptionI", currentOptionI);

                let compareOptionI = currentOptionI + 1;
                console.log("compareOptionI start", compareOptionI);
                while (compareOptionI < this.options.length) {
                    // get next option in list
                    console.log("current compare option i", compareOptionI);
                    tmpDecisions.push({
                        option1: option,
                        option2: this.options[compareOptionI],
                        decision: null,
                    });
                    compareOptionI += 1;
                }
                //}
            });
            console.log("tempdecisions", tmpDecisions);
            return tmpDecisions;
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
