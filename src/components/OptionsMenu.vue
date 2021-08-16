<template>
	<div class="options-wrap">
		<div class="options-relative">
			<div
				:class="[
					'options-menu-button',
					{ 'options-button-active': isMenuOpen },
				]"
				@click="isMenuOpen = true"
			>
				<img src="../assets/md-hamburger.svg" height="50" width="50" />
			</div>
			<div
				:class="['options-menu', { 'options-menu-active': isMenuOpen }]"
			>
				<button class="add-option-button" @click="localOptionsNumber++">
					Add option
				</button>
				<img
					class="menu-close-button"
					src="../assets/md-close.svg"
					height="35"
					width="35"
					@click="isMenuOpen = false"
				/>
			</div>
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
}

.options-relative {
	position: relative;
	padding: 3vh 3vh 0 0;
	transition: all 0.3s;
}

.options-menu-button {
	position: absolute;
	top: 0;
	right: 0;
	padding: 0.75rem;
	transition: inherit;
	z-index: 5;
}

.options-menu-button:hover {
	transform: scale(1.1);
}

.options-button-active {
	top: -70px;
	opacity: 0;
}

.options-menu {
	position: absolute;
	top: 100px;
	right: 0;
	transition: inherit;
	opacity: 0;
	width: 300px;
	background: #f9f9f9;
	border-left: 1rem solid rgba(0, 0, 0, 0.2);
	padding: 1rem;
	display: flex;
	align-items: center;
	justify-content: space-between;
}

.options-menu-active {
	top: 0;
	opacity: 1;
}

.menu-close-button {
	transition: inherit;
	padding: 0.25rem;
}

.menu-close-button:hover {
	transform: scale(1.1);
}

.add-option-button {
	font-family: "Roboto";
	border: 0;
	background: #e8e8e8;
	color: #444444;
	padding: 6px;
	transition: inherit;
}

.add-option-button:hover {
	background: #f2f2f2;
	color: #999999;
}
</style>