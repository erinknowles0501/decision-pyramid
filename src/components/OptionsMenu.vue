<template>
	<div class="options-wrap">
		<div
			:class="[
				'options-menu-button',
				{ 'options-button-active': isMenuOpen },
			]"
			@click="isMenuOpen = true"
		>
			<img src="../assets/md-hamburger.svg" height="50" width="50" />
		</div>
		<div :class="['options-menu', { 'options-menu-active': isMenuOpen }]">
			<img
				class="menu-close-button"
				src="../assets/md-close.svg"
				height="50"
				width="50"
				@click="isMenuOpen = false"
			/>
			<button @click="localOptionsNumber++">Add option</button>
		</div>
	</div>
</template>

<script>
export default {
	name: "options-menu",
	props: {
		value: {
			type: Number,
			required: true,
		},
	},
	data: () => ({
		isMenuOpen: false,
	}),
	computed: {
		localOptionsNumber: {
			get() {
				return this.value;
			},
			set(val) {
				this.$emit("input", val);
			},
		},
	},
};
</script>

<style scoped>
.options-wrap {
	position: fixed;
	top: 0;
	right: 0;
	padding: 3vh 3vh 0 0;
	z-index: 10;
	transition: all 0.3s;
}

.options-menu-button {
	position: absolute;
	top: 0;
	right: 0;
	padding: 12px;
	transition: inherit;
	z-index: 5;
}

.options-menu-button:hover {
	right: 10px;
}

.options-button-active {
	top: -50px;
	opacity: 0;
}

.options-menu {
	top: 100px;
	right: 0;
	transition: inherit;
	opacity: 0;
}

.options-menu-active {
	top: 0;
	opacity: 1;
}

.menu-close-button {
	float: absolute;
	top: 0;
	right: 0;
	z-index: 6;
}

.menu-close-button:hover {
	transition: inherit;
	transform: scale(1.1);
}
</style>