<template>
    <div>
        <div
            @click="settingOption = true"
            v-if="!settingOption"
            style="cursor: default"
        >
            {{ localOption ? localOption : "Click here to set an option" }}
        </div>
        <div v-if="settingOption">
            <input
                type="text"
                v-model="localOption"
                @blur="settingOption = false"
                @keydown.enter="settingOption = false"
                ref="input"
            />
        </div>
    </div>
</template>

<script>
export default {
    name: "decision-option",
    props: ["value"],
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