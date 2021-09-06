<template>
	<div id="app">
		<OptionsMenu v-model="optionsNumber" />
		<div id="app-wrap">
			<div id="rotate-wrap">
				<div :style="calcGridStyles">
					<DecisionOption
						v-for="option in options"
						:key="`option-${option.id}-${option.hsl.hue}`"
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
	</div>
</template>

<script>
const letters = ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"]; // More than this is lunacy. Locking maximum options to this length.

import DecisionOption from "./components/DecisionOption.vue";
import DecisionPicker from "./components/DecisionPicker.vue";
import OptionsMenu from "./components/OptionsMenu.vue";

export default {
	name: "App",
	components: {
		DecisionOption,
		DecisionPicker,
		OptionsMenu,
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
		calcGridStyles() {
			return (
				"position: relative; display: grid; grid-template-rows: 100px; grid-template-columns: " +
				"100px ".repeat(this.options.length)
			);
		},
	},
	created() {
		this.generateOptions();
		this.generateDecisions();
		this.generateColors();
	},
	methods: {
		makeNewOption(optionNumber) {
			return {
				text: "Click here to set an option",
				id: optionNumber + 1,
				letter: letters[optionNumber],
				hsl: {},
			};
		},
		getTextFromId(id) {
			return this.options.find((option) => option.id == id).text;
		},
		generateOptions() {
			let options = [];
			for (var i = 0; i < this.optionsNumber; i++) {
				options.push(this.makeNewOption(i));
			}
			this.options = options;
		},
		addOption() {
			if (this.options.length === letters.length) {
				// Simply too many. No.
				return;
				// TODO: Notification.
			}

			let tempOptions = this.options;
			tempOptions.push(this.makeNewOption(this.optionsNumber - 1));
			this.options = tempOptions;
			this.addNewDecisions();
		},
		generateColors() {
			const colorsNum = this.options.length;
			//console.log("options len!", this.options.length);
			let tempOptions = JSON.parse(JSON.stringify(this.options));
			console.log("tempoptions1", tempOptions);
			tempOptions = this.options.map((option, index) => {
				//console.log("index!", index);
				const optionHue = (360 / colorsNum) * index + 20; // 20 is arbitrary here, just want to avoid normal rainbow.
				//console.log("optionhue", optionHue);
				option.hsl.hue = optionHue;
				option.hsl.saturation = 70;
				option.hsl.lightness = 60;
				console.log("option", option);
				return option;
			});

			console.log(tempOptions);
			this.options = tempOptions;
			//console.log("this.options", this.options);
		},
		generateDecisions() {
			// for every option there is, create a decision object that compares that option
			// against every other option.

			// start at first option,
			// create decision object for option+[every option between currentOption+1 and options.length-1]
			// loop again at second option+[every option between currentOption+1 and options.length-1]
			// keep doing this until second-last option (last option will have no matches)

			// for Vue reactivity - instead of pushing directly to .decisions, set whole thing equal to this temp array.
			let tempDecisions = [];

			this.options.forEach((option) => {
				let compareOptionIteration = this.options.indexOf(option) + 1;

				while (compareOptionIteration < this.options.length) {
					tempDecisions.push({
						option1: option,
						option2: this.options[compareOptionIteration],
						decision: null,
					});
					compareOptionIteration += 1;
				}
			});
			this.decisions = tempDecisions;
		},
		addNewDecisions() {
			let tempDecisions = JSON.parse(JSON.stringify(this.decisions)); // Simplest/solidest way to copy an object without reference (ie, so altering object 2 doesn't alter object 1..!)
			const newOption = this.options[this.options.length - 1];

			// create decision objects for each intersection between existing option and new option.
			let compareOptionIteration = 0;
			while (compareOptionIteration < newOption.id - 1) {
				tempDecisions.push({
					option1: this.options[compareOptionIteration],
					option2: newOption,
					decision: null,
				});
				compareOptionIteration += 1;
			}
			this.decisions = tempDecisions;

			this.generateColors();
		},
		generatePickerHighlights() {
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
		},
	},
	watch: {
		currentHighlighted() {
			this.generatePickerHighlights();
		},
		decisions: {
			handler() {
				if (
					this.decisions.filter((decision) => !!decision.decision)
						.length === this.decisions.length
				) {
					this.currentHighlighted = {
						option: null,
						id: null,
						other: null,
					};
				}
			},
			deep: true,
		},
		optionsNumber(val, oldVal) {
			if (val > oldVal) {
				// Only add an option if the number is going up!
				this.addOption();
			}
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

#app-wrap {
	position: absolute;
	left: calc(50% - 250px);
	top: calc(150px + 10vh);
}

#rotate-wrap {
	transform: rotate(45deg);
	transform-origin: center center;
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
