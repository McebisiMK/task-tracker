<template>
  <h1>{{ title }}</h1>
  <Button
    @toggleOnClick="toggleAddTask"
    :text="getTaskButtonText(taskFormShown)"
    :color="getTaskButtonColor(taskFormShown)"
  />
</template>

<script>
import Button from "./Button.vue";

export default {
  name: "Header",
  props: {
    title: String,
    taskFormShown: Boolean,
  },
  components: {
    Button,
  },
  methods: {
    toggleAddTask() {
      this.$emit("toggleAddTask");
    },
    getTaskButtonText(taskFormShown) {
      return taskFormShown ? "Hide Form" : "Add Task";
    },
    getTaskButtonColor(taskFormShown) {
      return taskFormShown ? "red" : "green";
    },
  },
  emits: ["toggleAddTask"],
};
</script>

<style scoped>
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}
</style>
