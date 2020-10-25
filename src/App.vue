<template>
    <div id="app">
        <div
            :style="
                'display: grid; grid-template-columns: ' +
                '100px '.repeat(options.length)
            "
        >
            <DecisionOption
                v-for="option in options"
                :key="`option-${option.id}`"
                v-model="option.text"
                :id="option.id"
            />

            <DecisionPicker
                v-for="decision in decisions"
                :key="`decision-${decision.option1.id}-${decision.option2.id}`"
                v-model="decision.decision"
                :option1="decision.option1"
                :option2="decision.option2"
            />

            <div
                v-if="decisions"
                class="display-decisions"
                :style="`grid-column: ${options.length - 1} / span 2`"
            >
                <pre>{{ countVotesForEach }}</pre>
            </div>
        </div>
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
                { text: "one", id: 1 },
                { text: "two", id: 2 },
                { text: "three", id: 3 },
                { text: "four", id: 4 },
                { text: "five", id: 5 },
                { text: "six", id: 6 },
            ],
            decisions: null,
        };
    },
    computed: {
        countVotesForEach() {
            let votes = {};
            this.decisions.forEach((decision) => {
                if (decision.decision) {
                    if (!votes[decision.decision.id]) {
                        votes[decision.decision.id] = 1;
                    } else {
                        votes[decision.decision.id] += 1;
                    }
                }
            });
            return votes;
        },
    },
    created() {
        this.generateDecisions();
    },
    methods: {
        generateDecisions() {
            // for every option there is,
            // create a decision object that compares that option
            // against every other option.
            // prevent duplicates (ie entries for option1: A, option2: B AND option1: B, option2: A.)

            // start at first option
            // create decision object for option+[every option between currentOption+1 and optoins.length-1]
            // loop again at second option+[every option between currentOption+1 and options.length-1]
            // keep doing this until second-last option (last option will have no matches)

            // for Vue reactivity - instead of pushing directly to .decisions, set whole thing equal to this temp array.
            let tmpDecisions = [];

            this.options.forEach((option) => {
                let compareOptionI = this.options.indexOf(option) + 1;

                while (compareOptionI < this.options.length) {
                    tmpDecisions.push({
                        option1: option,
                        option2: this.options[compareOptionI],
                        decision: null,
                    });
                    compareOptionI += 1;
                }
            });
            this.decisions = tmpDecisions;
        },
    },
};
</script>

<style>
.option,
.picker {
    height: 100px;
    width: 100%;
    padding: 10px;
    border: 1px solid black;
    box-sizing: border-box;
}

.display-decisions {
    border: 1px solid black;
    padding: 1em;
    grid-row: 1 / span 2;
    background: rgba(255, 255, 255, 0.5);
    z-index: -10;
}
</style>
