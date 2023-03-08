<script>
export default {
  name: "SimpleInput",
  model: {
    prop: "value",
    event: "change",
  },
  props: {
    placeholder: String,
    type: { String, default: "text" },
    value: { String, default: "" },
    maxCaracters: { Number, default: 100 },
  },
  methods: {
    inputValidating(e) {
      var pattern = "[a-zA-Z0-9]";
      const char = String.fromCharCode(e.keyCode);
      if (char.match(pattern)) {
        console.log(char);
        return true;
      } else {
        console.log(char);
      }
    },
  },
};
</script>

<template>
  <input
    :type="type"
    :value="this.value"
    placeholder="Cidade"
    pattern="^[a-zA-Z0-9]+$"
    @keydown="inputValidating($event)"
    @keypress.enter="this.getWeatherByCity()"
  />
</template>
