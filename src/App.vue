<template>
    <div id="app">
        <div
            :style="
                'position: relative; display: grid; grid-template-columns: ' +
                '100px '.repeat(options.length)
            "
        >
            <DecisionOption
                v-for="option in options"
                :key="`option-${option.id}`"
                v-model="option.text"
                :id="option.id"
                :hsl="option.hsl"
                :isHighlighted="
                    currentHighlighted
                        ? currentHighlighted.id === option.id
                        : false
                "
            />

            <DecisionPicker
                v-for="decision in decisions"
                :key="`decision-${decision.option1.id}-${decision.option2.id}`"
                v-model="decision.decision"
                :option1="decision.option1"
                :option2="decision.option2"
                @highlight="currentHighlighted = $event"
                :isHighlighted="pickerHighlights.includes(decision)"
            />

            <div v-if="decisions" class="display-decisions">
                <p v-for="(vote, name) in countVotesForEach" :key="name">
                    <b>{{ getTextFromId(name) }}</b
                    >:
                    {{ vote }}
                </p>
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
                { text: "one", id: 1, hsl: {} },
                { text: "two", id: 2, hsl: {} },
                { text: "three", id: 3, hsl: {} },
                { text: "four", id: 4, hsl: {} },
                { text: "five", id: 5, hsl: {} },
                { text: "six", id: 6, hsl: {} },
            ],
            decisions: null,
            currentHighlighted: null,
            pickerHighlights: [],
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
        // TODO: Generate options based on default number of options.
        this.generateDecisions();
        this.generateColors();
    },
    methods: {
        getTextFromId(id) {
            return this.options.find((option) => option.id == id).text;
        },
        generateColors() {
            const colorsNum = this.options.length;
            this.options.map((option, index) => {
                option.hsl.hue = (360 / colorsNum) * index + 20; // 20 is arbitrary here, just want to avoid normal rainbow.
                option.hsl.saturation = 70;
                option.hsl.lightness = 60;
            });
        },
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
        generatePickerHighlights() {
            console.log("hi there!!");

            // if (!this.currentHighlight || !this.decisions) {
            //     return false;
            // }

            // generate array of picker combos to highlight based on whether
            // currentHighlighted.option === 1 && decision.option1 matches currerntHighlighted.id, and same for option2.
            // Also - if option1, decision option2 should be less than currentHighlighted other (same for opt2)
            let highlightedDecisions = [];
            this.decisions.forEach((decision) => {
                if (this.currentHighlighted.option === 1) {
                    if (
                        decision.option1.id === this.currentHighlighted.id &&
                        decision.option2.id < this.currentHighlighted.other
                    ) {
                        highlightedDecisions.push(decision);
                    }
                } else if (this.currentHighlighted.option === 2) {
                    if (
                        decision.option2.id === this.currentHighlighted.id &&
                        decision.option1.id > this.currentHighlighted.other
                    ) {
                        highlightedDecisions.push(decision);
                    }
                }
            });

            console.log(highlightedDecisions);
            this.pickerHighlights = highlightedDecisions;

            // return true if currenthighlighted is option 1 matches decision option 1 id, or option2+option2
            // const option1ShouldHighlight =
            //     this.currentHightlight.option === 1 &&
            //     decision.option1.id === this.currentHighlight.id;
            // const option2ShouldHighlight =
            //     this.currentHightlight.option === 2 &&
            //     decision.option2.id === this.currentHighlight.id;

            // console.log("option1?", option1ShouldHighlight);
            // console.log("option2?", option2ShouldHighlight);

            // return option1ShouldHighlight || option2ShouldHighlight;
        },
    },
    watch: {
        currentHighlighted() {
            this.generatePickerHighlights();
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
    box-sizing: border-box;
}

.display-decisions {
    padding: 1em;
    position: absolute;
    top: 0;
    right: 0;
    background: rgba(255, 255, 255, 0.5);
    z-index: -10;
    border: 1px solid rgba(0, 0, 0, 0.5);
}
</style>
