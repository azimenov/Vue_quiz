<template>
  <div class="task-list-container">
    <div class="task-input-container">
      <input v-model="newTaskTitle" placeholder="New task title" class="task-input" />
      <select v-model="newTaskPriority" class="task-priority-select">
        <option value="low">Low</option>
        <option value="medium">Medium</option>
        <option value="high">High</option>
      </select>
      <button @click="addTask" class="add-task-button">Add Task</button>
    </div>

    <transition-group name="list" tag="div" class="task-list">
      <TaskItem
          v-for="task in sortedTasks"
          :key="task.id"
          :task="task"
          @update:completed="markCompleted"
          :onDelete="deleteTask"
      />
    </transition-group>

    <p class="pending-tasks-count">Pending Tasks: {{ pendingTasksCount }}</p>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, nextTick, watch } from 'vue';
import TaskItem from './TaskItem.vue';
import { Task } from '@/interfaces/Task';

export default defineComponent({
  components: { TaskItem },
  setup() {
    const tasks = ref<Task[]>([]);
    const newTaskTitle = ref('');
    const newTaskPriority = ref<'low' | 'medium' | 'high'>('low');
    const nextId = ref(1);

    const addTask = async () => {
      if (newTaskTitle.value) {
        tasks.value.push({
          id: nextId.value++,
          title: newTaskTitle.value,
          priority: newTaskPriority.value,
          completed: false
        });
        newTaskTitle.value = '';
        await nextTick();
        document.getElementById(`task-${tasks.value.length - 1}`)?.scrollIntoView({ behavior: 'smooth' });
      }
    };

    const deleteTask = (taskId: number) => {
      tasks.value = tasks.value.filter(task => task.id !== taskId);
    };

    const markCompleted = (taskId: number) => {
      const task = tasks.value.find(t => t.id === taskId);
      if (task) task.completed = !task.completed;
    };

    const pendingTasksCount = computed(() => tasks.value.filter(task => !task.completed).length);

    const sortedTasks = computed(() => {
      return tasks.value.slice().sort((a, b) => {
        const priorityOrder = { high: 1, medium: 2, low: 3 };
        return priorityOrder[a.priority] - priorityOrder[b.priority];
      });
    });

    watch(
        tasks,
        (newTasks, oldTasks) => {
          if (newTasks.length > oldTasks.length) {
            console.log('Task added');
          }
        },
        { deep: true }
    );

    return {
      tasks,
      newTaskTitle,
      newTaskPriority,
      addTask,
      deleteTask,
      markCompleted,
      pendingTasksCount,
      sortedTasks,
    };
  }
});
</script>

<style scoped>
.task-list-container {
  max-width: 400px;
  margin: 0 auto;
  text-align: left;
}

.task-input-container {
  display: flex;
  gap: 8px;
  margin-bottom: 12px;
}

.task-input {
  flex-grow: 1;
  padding: 6px;
  border-radius: 4px;
  border: 1px solid #ccc;
}

.task-priority-select, .add-task-button {
  padding: 6px 10px;
  border-radius: 4px;
  border: 1px solid #ccc;
  cursor: pointer;
}

.add-task-button {
  background-color: #4CAF50;
  color: white;
}

.task-list {
  margin-top: 10px;
}

.pending-tasks-count {
  font-size: 14px;
  margin-top: 15px;
  color: #555;
}

.list-enter-active, .list-leave-active {
  transition: opacity 0.5s;
}
.list-enter, .list-leave-to {
  opacity: 0;
}
</style>
