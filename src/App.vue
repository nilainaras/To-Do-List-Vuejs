<template>
  <form action="" @submit.prevent="addTask">
    <fieldset role="group">
      <input v-model="task" type="text" placeholder="Add new task">
      <button :disabled="task.length == 0">Add</button>
    </fieldset>
  </form>
  <div v-if="todos.length == 0">You don't have any task to do</div>
  <div v-else>
    <ul>
      <li v-for="todo in todos" :key="task.date">
        <input type="checkbox" name="" id="">
        {{ todo.title }}</li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const todos = ref([]);
const task = ref('');
const addTask = () => {
  todos.value.push({
    title: task.value,
    completed: false,
    date: Date.now()
  });
  task.value = '';
}
</script>