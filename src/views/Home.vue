<template>
  <AddTask v-show="showAddTaskForm" @add-task="addTask" />
  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import yesno from "yesno-dialog";
import Tasks from "../components/Tasks";
import AddTask from "../components/AddTask";

export default {
  components: {
    AddTask,
    Tasks,
  },
  props: {
    showAddTaskForm: Boolean,
  },
  data() {
    return {
      tasks: [],
    };
  },
  methods: {
    async fetchTasks() {
      const response = await fetch("api/tasks");
      const data = response.json();

      return data;
    },
    async fetchTask(id) {
      const response = await fetch(`api/tasks/${id}`);
      const data = response.json();

      return data;
    },
    async deleteTask(id) {
      const yes = await yesno();
      if (yes) {
        const response = await fetch(`api/tasks/${id}`, {
          method: "DELETE",
        });

        response.status === 200
          ? (this.tasks = this.tasks.filter((task) => task.id !== id))
          : alert("Error deleting task!");
      }
    },
    async toggleReminder(id) {
      const task_to_toggle = await this.fetchTask(id);
      const updatedTask = {
        ...task_to_toggle,
        reminder: !task_to_toggle.reminder,
      };

      const response = await fetch(`api/tasks/${id}`, {
        method: "PUT",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(updatedTask),
      });

      const data = await response.json();

      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: data.reminder } : task
      );
    },
    async addTask(newTask) {
      const response = await fetch("api/tasks", {
        method: "POST",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(newTask),
      });

      const data = await response.json();
      this.tasks = [...this.tasks, data];
    },
  },
  async created() {
    this.tasks = await this.fetchTasks();
  },
};
</script>