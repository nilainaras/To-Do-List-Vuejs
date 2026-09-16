<template>
  <header class="todo-header" style="text-align: center;">
    <div class="logo-container">
      <svg class="todo-icon" xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 24 24" fill="none"
        stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <path d="M9 11l3 3L22 4"></path>
        <path d="M21 12v7a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11"></path>
      </svg>
    </div>
    <h1>My To-Do List</h1>
    <p class="subtitle">{{ todos.length }} task in total</p>
  </header>
  <form @submit.prevent="addTask">
    <fieldset role="group">
      <input v-model="task" type="text" placeholder="Add new task">
      <button :disabled="task.trim().length === 0" class="addNewTask">Add</button>
    </fieldset>
  </form>

  <fieldset class="filter">
    <button @click="currentFilter = 'all'">All Tasks</button>
    <button @click="currentFilter = 'done'">Task done</button>
    <button @click="currentFilter = 'todo'">Task to do</button>
  </fieldset>

  <div class="progress-container">
    <div class="progress-bar">
      <div class="progress-fill" :style="{ width: progressPercentage + '%' }"></div>
    </div>
    <span class="progress-text">{{ progressPercentage }}% Terminé</span>
  </div>

  <div v-if="filteredAndSortedTasks.length === 0" class="noTask">You don't have any task to do</div>
  <div v-else>
    <table>
      <tr v-for="todo in filteredAndSortedTasks" :key="todo.date" :class="{ completed: todo.completed }">
        <td><input type="checkbox" v-model="todo.completed"></td>
        <td>{{ todo.title }}</td>
        <td>{{ new Date(todo.date).toLocaleTimeString() }}</td>
        <td>
          <Trash :size="20" color="red" :stroke-width="2" @click="deleteTask(todo.date)" />
        </td>
      </tr>
    </table>
  </div>
</template>

<script setup>
import { Trash } from '@lucide/vue';
import { ref, computed, watch } from 'vue';

const savedTodos = localStorage.getItem('todos');
const todos = ref(savedTodos ? JSON.parse(savedTodos) : []);
const task = ref('');
const currentFilter = ref('all');

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal));
}, { deep: true });

const addTask = () => {
  todos.value.push({
    title: task.value,
    completed: false,
    date: Date.now()
  });
  task.value = '';
};

const filteredAndSortedTasks = computed(() => {
  let result = [...todos.value];

  if (currentFilter.value === 'done') {
    result = result.filter(t => t.completed);
  } else if (currentFilter.value === 'todo') {
    result = result.filter(t => !t.completed);
  }

  return result.sort((a, b) => (a.completed === b.completed ? 0 : a.completed ? 1 : -1));
});


const deleteTask = (taskDate) => {
  todos.value = todos.value.filter(task => task.date !== taskDate);
};

const progressPercentage = computed(() => {
  if (todos.value.length === 0) return 0;
  const completedCount = todos.value.filter(task => task.completed).length;
  return Math.round((completedCount / todos.value.length) * 100);
});
</script>

<style>
.todo-header {
  text-align: center;
  margin-bottom: 25px;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
}

.logo-container {
  display: inline-flex;
  justify-content: center;
  align-items: center;
  background: #42b983;
  border-radius: 50%;
  padding: 12px;
  margin-bottom: 10px;
  box-shadow: 0 4px 10px rgba(66, 185, 131, 0.3);
}

.todo-icon {
  color: white;
  animation: bounce 2s infinite;
}

.todo-header h1 {
  margin: 10px 0 5px;
  font-size: 2rem;
  color: white;
  font-weight: 700;
  letter-spacing: -0.5px;
}

.todo-header .subtitle {
  margin: 0;
  font-size: 0.95rem;
  color: #7f8c8d;
  font-weight: 400;
}

@keyframes bounce {

  0%,
  20%,
  50%,
  80%,
  100% {
    transform: translateY(0);
  }

  40% {
    transform: translateY(-5px);
  }

  60% {
    transform: translateY(-2px);
  }
}

.progress-container {
  max-width: 500px;
  /* Plus large pour s'adapter au titre */
  margin: 20px auto;
}

.completed {
  opacity: 0.5;
  text-decoration: line-through;
}

.filter {
  display: flex;
  gap: 5vw;
  align-items: center;
  justify-content: center;
  margin: 15px 0;
}

.addNewTask {
  background-color: goldenrod;
}

.progress-container {
  width: 100%;
  max-width: 400px;
  margin: 20px auto;
  text-align: center;
}

.progress-bar {
  width: 100%;
  height: 20px;
  background-color: #e0e0e0;
  border-radius: 10px;
  overflow: hidden;
  margin-bottom: 5px;
}

.progress-fill {
  height: 100%;
  background-color: #42b983;
  /* Vert Vue.js */
  border-radius: 10px;
  transition: width 0.5s ease-in-out;
}

.progress-text {
  font-size: 14px;
  color: #666;
}

.noTask {
  text-align: center;
}
</style>
