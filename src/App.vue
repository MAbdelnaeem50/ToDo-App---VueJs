<template>
  <div class="min-h-screen bg-gray-100 py-6 flex flex-col justify-center sm:py-12">
    <div class="relative py-3 sm:max-w-xl sm:mx-auto">
      <div class="absolute inset-0 bg-gradient-to-r from-cyan-400 to-light-blue-500 shadow-lg transform -skew-y-6 sm:skew-y-0 sm:-rotate-6 sm:rounded-3xl"></div>
      <div class="relative px-4 py-10 bg-white shadow-lg sm:rounded-3xl sm:p-20">
        <h1 class="text-4xl font-bold mb-8 text-center text-gray-800">Todo App</h1>

        <!-- Form section -->
        <form @submit.prevent="addTasks" class="mb-8">
          <div class="mb-4">
            <input type="text" v-model="todo.text" placeholder="Enter your task" required
              class="w-full px-3 py-2 placeholder-gray-300 border border-gray-300 rounded-md focus:outline-none focus:ring focus:ring-indigo-100 focus:border-indigo-300" />
          </div>
          <div class="flex mb-4 space-x-4">
            <input type="date" v-model="todo.from" placeholder="from" required
              class="w-1/2 px-3 py-2 placeholder-gray-300 border border-gray-300 rounded-md focus:outline-none focus:ring focus:ring-indigo-100 focus:border-indigo-300" />
            <input type="date" v-model="todo.to" placeholder="to" required
              class="w-1/2 px-3 py-2 placeholder-gray-300 border border-gray-300 rounded-md focus:outline-none focus:ring focus:ring-indigo-100 focus:border-indigo-300" />
          </div>
          <button type="submit"
            class="w-full px-3 py-4 text-white bg-indigo-500 rounded-md focus:bg-indigo-600 focus:outline-none">
            Add Task
          </button>
        </form>

        <!-- Tasks list section -->
        <div v-if="showTasks.length > 0" class="space-y-4">
          <div v-for="task in showTasks" :key="task.id" 
            class="p-4 border rounded-lg shadow-sm hover:shadow-md transition-shadow duration-300"
            :class="{ 'bg-green-100': task.isComplated, 'bg-white': !task.isComplated }">
            <div class="flex justify-between items-start">
              <div>
                <h3 class="text-lg font-semibold text-gray-800">{{ task.text }}</h3>
                <p class="text-sm text-gray-600">{{ task.from }} to {{ task.to }}</p>
                <p class="text-xs text-gray-500 mt-1">Created: {{ formatDate(task.createdAt) }}</p>
              </div>
              <div class="flex flex-col space-y-2">
                <button @click="complateTask(task.id)"
                  :class="task.isComplated ? 'bg-green-500 hover:bg-green-600' : 'bg-blue-500 hover:bg-blue-600'"
                  class="px-3 py-1 text-sm text-white rounded focus:outline-none focus:ring-2 focus:ring-opacity-50"
                  :style="{ minWidth: '90px' }">
                  {{ task.isComplated ? "Completed" : "Complete" }}
                </button>
                <button @click="deleteTask(task.id)"
                  class="px-3 py-1 text-sm text-white bg-red-500 rounded hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-opacity-50">
                  Delete
                </button>
              </div>
            </div>
          </div>
        </div>
        <h2 v-else class="text-xl font-semibold text-center text-gray-600 mt-8">
          No tasks found
        </h2>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";

const tasks = ref([]);
const todo = ref({
  id: "",
  text: "",
  from: "",
  to: "",
  createdAt: "",
  isComplated: false,
});
const showTasks = ref([]);

const addLocal = () => {
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
};

const addTasks = () => {
  todo.value.id = tasks.value.length + 1;
  todo.value.createdAt = new Date();
  tasks.value.push(todo.value);
  addLocal();
  alert("Task added successfully");
  todo.value = {
    text: "",
    from: "",
    to: "",
    createdAt: "",
    isComplated: false,
  };
  console.log(tasks.value);
};

const getLocal = () => {
  const task = localStorage.getItem("tasks");
  if (task) {
    tasks.value = JSON.parse(task);
  }
};

onMounted(() => {
  getLocal();
});

const showTask = () => {
  if (localStorage.getItem("tasks")) {
    showTasks.value = tasks.value; 
  }
};
onMounted(() => {
  showTask();
});

const complateTask = (id) => {
  const index = tasks.value.findIndex((task) => task.id === id);
  if (index !== -1) {
    tasks.value[index].isComplated = !tasks.value[index].isComplated;
    addLocal();
    showTask(); 
  }
};

const deleteTask = (id) => {
  const index = tasks.value.findIndex((task) => task.id === id);
  if (index !== -1) {
    tasks.value.splice(index, 1);
    addLocal();
    showTask(); 
  }
};

const formatDate = (dateString) => {
  const date = new Date(dateString);
  return date.toLocaleString();
};
</script>
