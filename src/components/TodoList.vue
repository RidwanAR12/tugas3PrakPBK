<template>
    <div>
      <h1>To-Do List</h1>
      <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add a new task"/>
      <ul>
        <li v-for="(todo, index) in todos" :key="index">
          <input type="checkbox" v-model="todo.completed" @change="saveTodos"/>
          <span :class="{ completed: todo.completed }">{{ todo.text }}</span>
          <button @click="editTodo(index)">Edit</button>
          <button @click="deleteTodo(index)">Delete</button>
        </li>
      </ul>
      <div v-if="editing !== null">
        <input v-model="editedText" @keyup.enter="updateTodo"/>
        <button @click="updateTodo">Update</button>
      </div>
      <p>Total tasks: {{ todos.length }}</p>
      <p>Incomplete tasks: {{ incompleteTodosCount }}</p>
    </div>
  </template>
  
  <script>
  import { ref, computed, onMounted } from 'vue';
  
  export default {
    setup() {
      const newTodo = ref('');
      const todos = ref([]);
      const editing = ref(null);
      const editedText = ref('');
  
      const incompleteTodosCount = computed(() => {
        return todos.value.filter(todo => !todo.completed).length;
      });
  
      const addTodo = () => {
        if (newTodo.value.trim() !== '') {
          todos.value.push({ text: newTodo.value, completed: false });
          newTodo.value = '';
          saveTodos();
        }
      };
  
      const deleteTodo = (index) => {
        todos.value.splice(index, 1);
        saveTodos();
      };
  
      const editTodo = (index) => {
        editing.value = index;
        editedText.value = todos.value[index].text;
      };
  
      const updateTodo = () => {
        if (editing.value !== null) {
          todos.value[editing.value].text = editedText.value;
          editing.value = null;
          editedText.value = '';
          saveTodos();
        }
      };
  
      const saveTodos = () => {
        localStorage.setItem('todos', JSON.stringify(todos.value));
      };
  
      const loadTodos = () => {
        const savedTodos = localStorage.getItem('todos');
        if (savedTodos) {
          todos.value = JSON.parse(savedTodos);
        }
      };
  
      onMounted(() => {
        loadTodos();
      });
  
      return {
        newTodo,
        todos,
        editing,
        editedText,
        incompleteTodosCount,
        addTodo,
        deleteTodo,
        editTodo,
        updateTodo,
      };
    }
  };
  </script>
  
  <style>
  .completed {
    text-decoration: line-through;
  }
  </style>
  