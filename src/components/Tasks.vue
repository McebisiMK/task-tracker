<template>
  <div :key="task.id" v-for="task in tasks">
    <Task
      @toggleReminder="toggleReminder(task.id)"
      @deleteTask="deleteTask(task.id)"
      :task="task"
    />
  </div>
</template>

<script>
import Task from "./Task.vue";

export default {
  name: "Tasks",
  props: {
    tasks: Array,
  },
  components: {
    Task,
  },
  methods: {
    deleteTask(taskId) {
      this.$emit("deleteTask", taskId);
    },
    toggleReminder(taskId) {
      this.$emit("toggleReminder", taskId);
    },
  },
  emits: ["deleteTask", "toggleReminder"],
};
</script>
