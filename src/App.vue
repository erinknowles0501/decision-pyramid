<template>
    <div id="app">
        <div id="app-wrap">
            <div
                :style="
                    'position: relative; display: grid; grid-template-rows: 100px; grid-template-columns: ' +
                    '100px '.repeat(options.length)
                "
            >
                <DecisionOption
                    v-for="option in options"
                    :key="`option-${option.id}`"
                    v-model="option.text"
                    :id="option.id"
                    :letter="option.letter"
                    :hsl="option.hsl"
                    :votes="countVotesForEach[option.id]"
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
            </div>
        </div>
    </div>
</template>

<script>
const letters = ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L"];

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
            options: [],
            optionsNumber: 5,
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
        this.generateOptions();
        this.generateDecisions();
        this.generateColors();
    },
    methods: {
        getTextFromId(id) {
            return this.options.find((option) => option.id == id).text;
        },
        generateOptions() {
            let options = [];
            for (var i = 0; i < this.optionsNumber; i++) {
                options.push({
                    text: "Click here to set an option",
                    id: i + 1,
                    letter: letters[i],
                    hsl: {},
                });
            }
            this.options = options;
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
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500&family=Trispace:wght@600&display=swap");

html,
body {
    height: 100%;
}

#app {
    position: absolute;
    right: 0;
}

#app-wrap {
    transform: rotate(45deg);
    transform-origin: top left;
}

.option,
.picker {
    height: 100px;
    width: 100%;
    padding: 10px;
    box-sizing: border-box;
    font-family: "Roboto", sans-serif;
    font-weight: 400;
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
