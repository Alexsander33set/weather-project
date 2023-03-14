<script>
export default {
  name: "SimpleInput",
  props: {
    type: { String, default: "text" },
    placeholder: { String, default: "" },
    modelValue: { String, default: "default", required: true },
  },
  methods: {
    updateValue(event) {
      this.$emit("update:modelValue", event.target.value);
    },
  },
};
</script>

<template>
  <input
    :type="type"
    pattern="^[a-zA-Z0-9]+$"
    :placeholder="placeholder"
    :value="modelValue"
    @keypress.enter="updateValue($event)"
  />
</template>
