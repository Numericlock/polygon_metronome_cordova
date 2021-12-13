<template>
  <label
    >{{ label }}
    <input type="text" :value="value" @input="updateValue($event.target.value)" />
  </label>
</template>

<script>
import { defineComponent } from "@vue/composition-api";

export default defineComponent({
  props: {
    label: {
      require: true,
      type: String
    },
    value: {
      require: true,
      type: String
    }
  },
  setup(_, { emit }) {
    const updateValue = (value) => emit("update:value", value);
    return {
      updateValue
    };
  }
});
</script>