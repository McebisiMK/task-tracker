<template>
  <TaskForm v-show="showTaskForm" @addTask="addTask" />
  <Tasks
    @toggleReminder="toggleReminder"
    @deleteTask="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import Tasks from "../components/Tasks.vue";
import TaskForm from "../components/TaskForm.vue";

export default {
  name: "Home",
  props: {
    showTaskForm: Boolean,
  },
  components: {
    Tasks,
    TaskForm,
  },
  data() {
    return {
      tasks: Array,
    };
  },
  methods: {
    async addTask(taskData) {
      const response = await fetch(
        "api/tasks",
        this.getRequestPayload("POST", taskData)
      );

      const task = await response.json();

      this.tasks = [...this.tasks, task];
    },
    async getTasks() {
      const response = await fetch("api/tasks");
      const tasks = await response.json();

      return tasks;
    },
    async getTask(id) {
      const response = await fetch(`api/tasks/${id}`);
      const task = await response.json();

      return task;
    },
    async toggleReminder(taskId) {
      const taskToUpdate = await this.getTask(taskId);
      const updatedTask = { ...taskToUpdate, reminder: !taskToUpdate.reminder };

      const response = await fetch(
        `api/tasks/${taskId}`,
        this.getRequestPayload("PUT", updatedTask)
      );

      response.status === 200
        ? this.updateReminder(await response.json())
        : this.alertUser("updating");
    },
    async deleteTask(id) {
      if (this.deleteConfirmed()) {
        const response = await fetch(`api/tasks/${id}`, { method: "DELETE" });

        response.status === 200
          ? this.removeDeletedTask(id)
          : this.alertUser("deleting");
      }
    },
    removeDeletedTask(id) {
      this.tasks = this.tasks.filter((task) => task.id !== id);
    },
    deleteConfirmed() {
      return confirm("Are you sure you want to DELETE this task?");
    },
    updateReminder(updatedTask) {
      this.tasks = this.tasks.map((task) =>
        task.id === updatedTask.id
          ? { ...task, reminder: updatedTask.reminder }
          : task
      );
    },
    getRequestPayload(requestMethod, requestBody) {
      return {
        method: `${requestMethod}`,
        headers: { "Content-type": "application/json" },
        body: JSON.stringify(requestBody),
      };
    },
    alertUser(operation) {
      alert(`Error occurred while ${operation} a task`);
    },
  },
  async created() {
    this.tasks = await this.getTasks();
  },
};
</script>
