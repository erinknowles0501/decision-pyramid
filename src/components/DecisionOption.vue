<template>
    <div class="option" :style="`grid-row: ${id}; grid-column: ${id}`">
        <div
            @click="settingOption = true"
            v-if="!settingOption"
            class="display-option"
        >
            {{ localOption ? localOption : "Click here to set an option" }}
        </div>
        <div v-if="settingOption" style="height: 100%">
            <textarea
                v-model="localOption"
                @blur="settingOption = false"
                @keydown.enter="settingOption = false"
                ref="input"
                class="set-option"
            />
        </div>
    </div>
</template>

<script>
export default {
    name: "decision-option",
    props: ["value", "id"],
    data() {
        return {
            settingOption: false,
        };
    },
    computed: {
        localOption: {
            get() {
                return this.value;
            },
            set(val) {
                this.$emit("input", val);
            },
        },
    },
    watch: {
        settingOption(val) {
            if (val === true) {
                this.$nextTick(() => {
                    this.$refs.input.focus();
                    this.$refs.input.select();
                });
            }
        },
    },
};
</script>

<style scoped>
.option {
    background-color: red;
}

.display-option {
    cursor: default;
    height: 100%;
    width: 100%;
    box-sizing: border-box;
    overflow: hidden;
    text-overflow: ellipsis;
}

.set-option {
    height: 100%;
    width: 100%;
    resize: none;
    box-sizing: border-box;
    border: 0;
    background: rgba(255, 255, 255, 0.4);
}
</style>