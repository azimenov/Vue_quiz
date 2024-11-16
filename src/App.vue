<template>
  <div id="app">
    <h1>Animated To-Do List</h1>
    <TaskList />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import TaskList from './components/TaskList.vue';

export default defineComponent({
  components: {
    TaskList
  }
});
</script>

<style>
#app {
  text-align: center;
  margin-top: 20px;
}
</style>

