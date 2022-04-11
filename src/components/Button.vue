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
      console.log("Added new task");
    },
  },
};
</script>
