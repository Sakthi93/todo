<template>
  <div class="p-6 max-w-xl mx-auto">
    <h1 class="text-2xl font-bold mb-4">📝 ToDo List</h1>

    <form @submit.prevent="addTask" class="mb-4 flex">
      <input v-model="newTask.title" type="text" placeholder="Enter task" class="border p-2 w-full mr-2" />
      <button type="submit" class="bg-blue-600 text-white px-4 py-2">Add</button>
    </form>

    <div v-for="task in tasks" :key="task.id" class="flex justify-between items-center border p-2 mb-2">
      <div>
        <input type="checkbox" v-model="task.completed" @change="updateTask(task)" />
        <span :class="{ 'line-through': task.completed }" class="ml-2">{{ task.title }}</span>
      </div>
      <button @click="deleteTask(task.id)" class="text-red-600">Delete</button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const tasks = ref([])
const newTask = ref({ title: '', completed: false })

const API = 'http://localhost:8000/tasks' // Update to your backend host if different

const fetchTasks = async () => {
  const res = await axios.get(API)
  tasks.value = res.data
}

const addTask = async () => {
  if (!newTask.value.title) return
  const id = tasks.value.length ? Math.max(...tasks.value.map(t => t.id)) + 1 : 1
  const task = { id, ...newTask.value }
  await axios.post(API, task)
  newTask.value.title = ''
  fetchTasks()
}

const updateTask = async (task) => {
  await axios.put('${API}/${task.id}', task)
  fetchTasks()
}

const deleteTask = async (id) => {
  await axios.delete('${API}/${id}')
  fetchTasks()
}

onMounted(fetchTasks)
</script>

<style>
.line-through {
  text-decoration: line-through;
}
</style>