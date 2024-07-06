<script setup lang="ts">
import Swal from 'sweetalert2';
import { reactive, onMounted } from 'vue';
import TodoCard from './components/TodoCards.vue'
// Define interfaces for Todo and AppState
interface Todo {
  title: string;
  done: boolean;
}

interface AppState {
  todos: Todo[];
  newTodo: string;
  name: string;
}

// Create reactive state based on AppState interface
const state = reactive<AppState>({
  todos: [],
  newTodo: '',
  name: ''
});

// Function to add a new todo
function addTodo() {
  if (state.newTodo.trim()) {
    state.todos.push({ title: state.newTodo, done: false });
    state.newTodo = '';
    saveToLocalStorage(); // Save to localStorage after adding todo
  }
}

// Function to mark a todo as done
function markDone(index: number) {
  state.todos[index].done = !state.todos[index].done;
  saveToLocalStorage(); // Save to localStorage after marking todo as done
}

// Function to delete a todo
function deleteTodo(index: number) {
  state.todos.splice(index, 1);
  saveToLocalStorage(); // Save to localStorage after deleting todo
}

async function deleteAll(){
  const result = await Swal.fire({
  title: "Are you sure?",
  text: "You won't be able to revert this!",
  icon: "warning",
  showCancelButton: true,
  confirmButtonColor: "#3085d6",
  cancelButtonColor: "#d33",
  confirmButtonText: "Yes, delete it!"
});

if (result.isConfirmed) {
    Swal.fire({
      title: "Deleted!",
      text: "Your Todo has been cleared!.",
      icon: "success"
    });
    state.todos.splice(0, state.todos.length);
   saveToLocalStorage();
  }

}

// Function to save state to localStorage
function saveToLocalStorage() {
  localStorage.setItem('appState', JSON.stringify(state));
}

// Function to load state from localStorage
function loadFromLocalStorage() {
  const savedState = localStorage.getItem('appState');
  if (savedState) {
    Object.assign(state, JSON.parse(savedState));
  }
}

// Load state from localStorage when component is mounted
onMounted(() => {
  loadFromLocalStorage();
});
</script>

<template>
  <header>
    <nav class="flex justify-center h-[100px] bg-purple-300">
      <div class="w-[1240px] flex items-center">
        <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="100" height="100" />
        <h2 class="ml-2 text-3xl font-bold text-white">ToDoList</h2>
      </div>
    </nav>
  </header>

  <main>
    <div class="p-5">
      <!-- Greetings -->
      <div class="greetings">
        <h2 class="text-2xl font-bold text-gray-500">Hello,<input type="text" v-model="state.name" placeholder="Name Here"/></h2>
      </div>

      <!-- Add ToDo Form -->
      <div class="mt-2">
        <form class="w-full max-w-sm" @submit.prevent="addTodo">
          <h1 class="text-xl text-gray-300">What's on your mind?</h1>
          <div class="md:flex md:items-center mb-6">
            <div class="md:w-2/3">
              <input
                v-model="state.newTodo"
                class="bg-gray-200 appearance-none border-2 border-gray-200 rounded w-full py-2 px-4 text-gray-700 leading-tight focus:outline-none focus:bg-white focus:border-purple-500"
                id="inline-full-name"
                type="text"
                placeholder="Enter new todo"
              />
            </div>
          </div>
          <div class="md:flex md:items-center">
            <div class="md:w-2/3">
              <button class="shadow bg-purple-500 hover:bg-purple-400 focus:shadow-outline focus:outline-none text-white font-bold py-2 px-4 rounded" type="submit">
                <img src="./assets/plus.png" alt=""> Add
              </button>
              <button class="ml-2 shadow bg-red-500 hover:bg-red-400 focus:shadow-outline focus:outline-none text-white font-bold py-2 px-4 rounded" @click="deleteAll"><img class="ml-3" src="./assets/delete.png" alt=""> Delete All</button>
            </div>
          </div>
        </form>
      </div>

      <!-- ToDo Cards -->
      <section class="row flex flex-wrap gap-4 p-4">
        <TodoCard
          v-for="(todo, index) in state.todos"
          :key="index"
          :activity="todo.title"
          :done="todo.done"
          @done="markDone(index)"
          @delete="deleteTodo(index)"
        />
      </section>
    </div>
  </main>
</template>

<style>
/* Your styles here */
</style>
