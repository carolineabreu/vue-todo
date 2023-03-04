<script setup>
import { computed, onMounted, ref, watch } from 'vue';

const todos = ref([]);
const name = ref("");

const inputContent = ref("");
const inputCategory = ref("");

const todosAsc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt;
}));

function addTodo() {
  if (inputContent.value.trim() === "" || inputCategory.value === null) {
    return;
  }

  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime()
  });
}

function removeTodo(todo) {
  todos.value = todos.value.filter((currentTodo) => currentTodo !== todo);
}

watch(todos, (newValue) => {
  localStorage.setItem("todos", JSON.stringify(newValue));
}, { deep: true });

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">What's up, <input type="text" placeholder="name here" v-model="name" /></h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="e.g. make a video" v-model="inputContent" />

        <h4>Pick a category</h4>
        <div class="options">
          <label for="category1">
            <input type="radio" name="category" id="category1" value="work" v-model="inputCategory" />
            <span class="bubble business"></span>
            <div>Work</div>
          </label>
          <label for="category2">
            <input type="radio" name="category" id="category2" value="personal" v-model="inputCategory" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
          <label for="category3">
            <input type="radio" name="category" id="category3" value="home" v-model="inputCategory" />
            <span class="bubble home"></span>
            <div>Home</div>
          </label>
          <label for="category4">
            <input type="radio" name="category" id="category4" value="projects" v-model="inputCategory" />
            <span class="bubble projects"></span>
            <div>Projects</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todosAsc" :class="`todo-item ${todo.done && done}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
