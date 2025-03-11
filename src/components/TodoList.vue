<template>
  <div class="p-6 ">
    <h1 class="text-3xl font-bold text-center text-blue-600 mb-6">Task List</h1>

    <!-- New Task Form -->
    <form @submit.prevent="addTodo" class="flex items-center gap-3 mb-6 max-w-xl mx-auto">
      <input
        v-model="newTodo.title"
        placeholder="New Task"
        required
        class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
      />
      <button
        type="submit"
        class="bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition flex items-center"
        :disabled="loading"
      >
        <fwb-spinner v-if="loading" color="green" class="m-1" />
        <span v-else>Add</span>
      </button>
    </form>

    <!-- Task List -->
    <ul class="space-y-3 max-w-xl mx-auto">
      <li
        v-for="todo in todos"
        :key="todo.id"
        class="flex justify-between items-center bg-white p-4 rounded-lg shadow-md border border-gray-200"
      >
        <span
          @click="editTodo(todo)"
          :style="{ textDecoration: todo.completed ? 'line-through' : 'none' }"
          class="cursor-pointer text-lg font-medium"
        >
          {{ todo.title }}
        </span>
        <div class="flex gap-2">
          <button
            @click="deleteTodo(todo.id)"
            class="bg-red-500 hover:bg-red-600 text-white py-1 px-3 rounded-lg transition"
            :disabled="loading"
          >
            <fwb-spinner v-if="loading" color="green" class="m-1" />
            <svg v-else class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
              <path fill-rule="evenodd" d="M2 12C2 6.477 6.477 2 12 2s10 4.477 10 10-4.477 10-10 10S2 17.523 2 12Zm5.757-1a1 1 0 1 0 0 2h8.486a1 1 0 1 0 0-2H7.757Z" clip-rule="evenodd"/>
            </svg>
          </button>
          <button
            @click="toggleComplete(todo)"
            class="bg-green-500 hover:bg-green-600 text-white py-1 px-3 rounded-lg transition"
            :disabled="loading"
          >
            <fwb-spinner v-if="loading" color="green" class="m-1" />
            <span v-else>
              <span v-if="!todo.completed">
                <svg class="w-6 h-6 text-gray-800 dark:text-white inline" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
                  <rect width="20" height="20" x="2" y="2" rx="3" ry="3"></rect>
                </svg>
              </span>
              <span v-else>
                <svg class="w-6 h-6 text-gray-800 dark:text-white inline" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
                  <path fill-rule="evenodd" d="M9 11.293l-3.293-3.293a1 1 0 1 1 1.414-1.414l2.293 2.293 4.293-4.293a1 1 0 1 1 1.414 1.414l-5 5a1 1 0 0 1-1.414 0z" clip-rule="evenodd"/>
                  <rect width="20" height="20" x="2" y="2" rx="3" ry="3" fill="none" stroke="currentColor" stroke-width="2"></rect>
                </svg>
              </span>
            </span>
          </button>
        </div>
      </li>
    </ul>

    <!-- Edit Task Form -->
    <div
      v-if="editingTodo"
      class="mt-8 max-w-xl mx-auto bg-white p-6 rounded-lg shadow-md border border-gray-200"
    >
      <h2 class="text-2xl font-semibold text-gray-800 mb-4">Edit Task</h2>
      <form @submit.prevent="updateTodo" class="space-y-4">
        <input
          v-model="editingTodo.title"
          required
          class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
        <div class="flex justify-end gap-3">
          <button
            type="submit"
            class="bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition"
            :disabled="loading"
          >
            <fwb-spinner v-if="loading" color="green" class="m-1" />
            <span v-else>Save</span>
          </button>
          <button
            @click="cancelEdit"
            class="bg-gray-300 hover:bg-gray-400 text-gray-800 py-2 px-4 rounded-lg transition"
          >
            Cancel
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from "vue";
import axios from "axios";
import { FwbSpinner } from "flowbite-vue";

// API Base URL
const API_BASE_URL = "https://django-vuejs-todo-backend.onrender.com/api/todos/";

// State
const todos = ref([]);
const newTodo = reactive({ title: "", completed: false });
const editingTodo = ref(null);
const loading = ref(false);

// Fetch Tasks
const fetchTodos = async () => {
  try {
    const response = await axios.get(API_BASE_URL, {
      headers: { "Content-Type": "application/json" },
      withCredentials: true,
    });
    todos.value = response.data;
  } catch (error) {
    console.error("Error fetching tasks:", error);
  }
};

// Add New Task
const addTodo = async () => {
  if (!newTodo.title.trim()) return;
  loading.value = true;

  try {
    const response = await axios.post(API_BASE_URL, newTodo, {
      headers: { "Content-Type": "application/json" },
      withCredentials: true,
    });
    todos.value.push(response.data);
    newTodo.title = "";
  } catch (error) {
    console.error("Error adding task:", error);
  } finally {
    loading.value = false;
  }
};

// Delete Task
const deleteTodo = async (id) => {
  loading.value = true;
  try {
    await axios.delete(`${API_BASE_URL}${id}/`, {
      headers: { "Content-Type": "application/json" },
      withCredentials: true,
    });
    todos.value = todos.value.filter((todo) => todo.id !== id);
  } catch (error) {
    console.error("Error deleting task:", error);
  } finally {
    loading.value = false;
  }
};

// Toggle Task Completion
const toggleComplete = async (todo) => {
  loading.value = true;
  try {
    const response = await axios.patch(
      `${API_BASE_URL}${todo.id}/`,
      { completed: !todo.completed },
      {
        headers: { "Content-Type": "application/json" },
        withCredentials: true,
      }
    );
    todo.completed = response.data.completed;
  } catch (error) {
    console.error("Error updating task:", error);
  } finally {
    loading.value = false;
  }
};

// Edit Task
const editTodo = (todo) => {
  editingTodo.value = { ...todo };
};

// Update Edited Task
const updateTodo = async () => {
  loading.value = true;
  try {
    const response = await axios.put(
      `${API_BASE_URL}${editingTodo.value.id}/`,
      editingTodo.value,
      {
        headers: { "Content-Type": "application/json" },
        withCredentials: true,
      }
    );
    const index = todos.value.findIndex((todo) => todo.id === response.data.id);
    todos.value[index] = response.data;
    editingTodo.value = null;
  } catch (error) {
    console.error("Error updating task:", error);
  } finally {
    loading.value = false;
  }
};

// Cancel Edit
const cancelEdit = () => {
  editingTodo.value = null;
};

// Fetch tasks on mount
onMounted(fetchTodos);
</script>
