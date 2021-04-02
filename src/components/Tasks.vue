<template>
  <div>
    <div v-for="task in tasks" :key="task.id">
      <TaskItem
        @toggle-reminder="$emit('toggle-reminder', task.id)"
        @delete-task="$emit('delete-task', task.id)"
        :task="task"
      />
    </div>
  </div>
</template>

<script>
import TaskItem from "./TaskItem";
export default {
  name: "Tasks",
  components: {
    TaskItem,
  },
  props: {
    tasks: Array,
  },
  emits: ["delete-task", "toggle-reminder"],
};
</script>
