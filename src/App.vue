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
