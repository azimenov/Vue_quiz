<template>
  <transition name="task-fade" mode="out-in">
    <div :class="['task-container', task.priority + '-priority', { completed: task.completed }]">
      <!-- Checkbox to mark as completed -->
      <input type="checkbox" :checked="task.completed" @change="toggleComplete" />
      <span @dblclick="editTitle">{{ task.title }}</span>
      <button @click="deleteTask">Delete</button>
    </div>
  </transition>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';
import { Task } from '@/interfaces/Task';

export default defineComponent({
  props: {
    task: {
      type: Object as PropType<Task>,
      required: true,
    },
    onDelete: Function as PropType<(taskId: number) => void>,
  },
  methods: {
    toggleComplete() {
      this.$emit('update:completed', this.task.id);
    },
    deleteTask() {
      this.onDelete && this.onDelete(this.task.id);
    },
  },
});
</script>

<style scoped>
@import '@/styles/taskStyles.css';
</style>
