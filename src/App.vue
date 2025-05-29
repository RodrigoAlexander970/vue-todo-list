<script setup lang="ts">
import AppHeader from './components/AppHeader.vue'
import TodoList from './components/TodoList.vue'
</script>

<template>
  <div class="app">
    <AppHeader />
    <TodoList />
  </div>
</template>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: #f7fafc;
  color: #2d3748;
  line-height: 1.6;
}

.app {
  min-height: 100vh;
  background: linear-gradient(180deg, #f7fafc 0%, #edf2f7 100%);
}

#app {
  margin: 0;
  padding: 0;
}
</style>