<template>
  <div>
    <AddTask v-if="showAddTask" @add-task="addTask" />

    <Tasks
      :tasks="tasks"
      @toggle-reminder="toggleReminder"
      @delete-task="deleteTask"
    />
  </div>
</template>

<script>
import Tasks from "../components/Tasks";
import AddTask from "../components/AddTask";
import axios from "axios";
export default {
  name: "Home",
  components: {
    Tasks,
    AddTask,
  },
  props: {
    showAddTask: Boolean,
  },
  data() {
    return {
      tasks: [],
    };
  },
  async created() {
    this.tasks = await this.fetchTasks();
  },
  methods: {
    async addTask(task) {
      //this.tasks = [...this.tasks, task];
      const data = await axios
        .post("api/tasks", {
          ...task,
        })
        .then((res) => res.data)
        .catch((error) => alert(error));
      return (this.tasks = [...this.tasks, data]);
    },
    async deleteTask(id) {
      if (confirm("Are you sure?")) {
        // this.tasks = this.tasks.filter((task) => {
        //   return task.id !== id;
        // });
        const res = await axios.delete(`/api/tasks/${id}`);
        if (res.status === 200) {
          this.tasks = this.tasks.filter((task) => {
            return task.id !== id;
          });
        } else {
          alert("Error deleting task");
        }
      }
    },
    async toggleReminder(id) {
      const taskToToggle = await this.fetchSingleTask(id);
      const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

      const data = await axios
        .put(`api/tasks/${id}`, updTask)
        .then((res) => res.data)
        .catch((err) => alert(err));

      this.tasks = this.tasks.map((task) => {
        if (task.id === id) {
          return { ...task, reminder: data.reminder };
        }
        return task;
      });
    },
    async fetchTasks() {
      //const res = await fetch("http://localhost:5000/tasks");
      const res = axios
        .get("/api/tasks")
        .then((res) => res.data)
        .catch((err) => alert(err));

      //const data = await res.json();
      return res;
    },
    async fetchSingleTask(id) {
      const res = axios
        .get(`/api/tasks/${id}`)
        .then((res) => res.data)
        .catch((err) => alert(err));
      //const data = await res.json();
      return res;
    },
  },
};
</script>
