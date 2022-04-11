<template>
  <div class="container">
    <Header
      @toggleAddTask="toggleAddTask"
      :taskFormShown="showTaskForm"
      title="Task Tracker"
    />
    <div v-show="showTaskForm">
      <TaskForm @addTask="addTask" />
    </div>
    <Tasks
      @toggleReminder="toggleReminder"
      @deleteTask="deleteTask"
      :tasks="tasks"
    />
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import Tasks from "./components/Tasks.vue";
import TaskForm from "./components/TaskForm.vue";

export default {
  name: "App",
  components: {
    Header,
    Tasks,
    TaskForm,
  },
  data() {
    return {
      tasks: [],
      showTaskForm: false,
    };
  },
  methods: {
    async addTask(taskData) {
      const response = await fetch("api/tasks", {
        method: "POST",
        headers: { "Content-type": "application/json" },
        body: JSON.stringify(taskData),
      });

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
    async deleteTask(id) {
      if (this.deleteConfirmed()) {
        const response = await fetch(`api/tasks/${id}`, { method: "DELETE" });

        response.status === 200 ? this.removeDeletedTask(id) : this.alertUser();
      }
    },
    removeDeletedTask(id) {
      this.tasks = this.tasks.filter((task) => task.id !== id);
    },
    deleteConfirmed() {
      return confirm("Are you sure you want to DELETE this task?");
    },
    alertUser() {
      alert("Error deleting task");
    },
    toggleReminder(taskId) {
      this.tasks = this.tasks.map((task) =>
        task.id === taskId ? { ...task, reminder: !task.reminder } : task
      );
    },
    toggleAddTask() {
      this.showTaskForm = !this.showTaskForm;
    },
  },
  async created() {
    this.tasks = await this.getTasks();
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: "Poppins", sans-serif;
}

.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}

.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}

.btn:focus {
  outline: none;
}

.btn:active {
  transform: scale(0.98);
}

.btn-block {
  display: block;
  width: 100%;
}
</style>
