<template>
  <button @click="onAddTask()" :style="{ background: color }" class="btn">
    {{ text }}
  </button>
</template>

<script>
export default {
  name: "Button",
  props: {
    text: String,
    color: String,
  },
  methods: {
    onAddTask() {
      this.$emit("toggleOnClick")
    },
  },
};
</script>
