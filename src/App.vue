<script setup>
  import { remove } from '@vue/shared';
import { ref, onMounted, computed, watch } from 'vue';

  const todos = ref([]);
  const name = ref('');

  const input_content = ref('');
  const input_category = ref(null);
  
  const todos_ascending = computed(() => todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  }));

  const addTodo = () => {
    if(input_content.value.trim() === '' || input_category.value === null) {
      return;
    };

    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      createdAt: new Date().getTime()
    });
    
    input_category.value = null;
    input_content.value = '';
  };

  const removeTodo = (todo) => {
    todos.value = todos.value.filter(t => t !== todo);
  };

  watch(name, (newValue) => {
    localStorage.setItem('name', newValue);

  });

  watch(todos, newValue => {
    localStorage.setItem('todos', JSON.stringify(newValue))}, {deep: true}
  );

  onMounted(() => {
    name.value = localStorage.getItem('name') || '';
    todos.value = JSON.parse(localStorage.getItem('todos')) || [];
  });

  


</script>

<template>
  <main class="app">
      <section class="greeting">
        <h2 class="title">
          What's up, <input type="text" name="" id="" placeholder="Name here" v-model="name">
        </h2>
      </section>

      <section class="create-todo">
        <h3>CREATE A TODO</h3>

        <form @submit.prevent="addTodo">
          <h4>What's on your todo list?</h4>
          <input type="text" name="" id="" placeholder="e.g. make a website" v-model="input_content">

          <h4>Pick a category</h4>
          <div class="options">
            <label>
              <input type="radio" name="category" value="business" v-model="input_category">
              <span class="bubble business"></span>
              <div>Business</div>
            </label>
            <label>
              <input type="radio" name="category" value="personal" v-model="input_category">
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>
          </div>

          <input type="submit" value="Add todo">
        </form>
      </section>

      <section class="todo-list">
        <h3>TODO LIST</h3>

        <div class="list">
          <div v-for="(todo, index) in todos_ascending" v-bind:key="index" v-bind:class="`todo-item ${todo.done}`" >

            <label>
              <input type="checkbox" v-model="todo.done">
              <span :class="`bubble ${todo.category}`"></span>
            </label>

            <div class="todo-content">
              <input type="text" name="" id="" v-model="todo.content">
            </div>

            <div class="actions">
              <button class="delete" v-on:click="removeTodo(todo)">Delete</button>
            </div>
          </div>
        </div>
      </section>
  </main>
</template>
