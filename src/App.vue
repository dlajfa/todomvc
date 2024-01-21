<script setup>
import { ref, computed } from "vue";

let newId = ref(1);
const newTodo = ref("");
const todoList = ref([]);
let itemFilter = ref("all");

const activeItems = computed(() => {
  return todoList.value.filter((item) => !item.completed);
});

const completedItems = computed(() => {
  return todoList.value.filter((item) => item.completed);
});

const itemsToShow = computed(() => {
  if (itemFilter.value === "all") {
    return todoList.value;
  } else if (itemFilter.value === "active") {
    return activeItems.value;
  } else if (itemFilter.value === "completed") {
    return completedItems.value;
  } else {
    return [];
  }
});

function addItem() {
  todoList.value.push({
    id: newId.value++,
    content: newTodo.value,
    completed: false,
    editing: false,
  });
  newTodo.value = "";
}

function removeItem(toRemove) {
  todoList.value = todoList.value.filter((item) => item.id !== toRemove.id);
}

function toggleAll() {
  if (activeItems.value.length) {
    todoList.value.forEach((item) => {
      if (!item.completed) {
        item.completed = true;
      }
    });
  } else {
    todoList.value.forEach((item) => {
      item.completed = false;
    });
  }
}

function clearCompleted() {
  todoList.value = activeItems.value;
}
</script>

<template>
  <header class="header">
    <h1>todos</h1>
    <input
      class="new-todo"
      v-model="newTodo"
      @keyup.enter="addItem"
      placeholder="What needs to be done?"
      autofocus
    />
  </header>

  <section v-show="todoList.length" class="main">
    <input
      id="toggle-all"
      @click="toggleAll"
      class="toggle-all"
      type="checkbox"
    />

    <label for="toggle-all">Mark all as complete</label>
    <ul class="todo-list">
      <li
        v-for="item in itemsToShow"
        :key="item.id"
        @dblclick="item.editing = true"
        @keyup.enter="item.editing = false"
        :class="{ editing: item.editing, completed: item.completed }"
      >
        <div class="view">
          <input class="toggle" type="checkbox" v-model="item.completed" />
          <label>{{ item.content }}</label>
          <button @click="removeItem(item)" class="destroy"></button>
        </div>
        <input
          v-if="item.editing"
          @vue:mounted="({ el }) => el.focus()"
          class="edit"
          @blur="item.editing = false"
          v-model="item.content"
        />
      </li>
    </ul>
  </section>

  <footer v-show="todoList.length" class="footer">
    <span class="todo-count">
      <strong>{{ activeItems.length }}</strong>
      {{ activeItems.length === 1 ? "item" : "items" }}
      left</span
    >
    <ul class="filters">
      <li>
        <a
          @click="itemFilter = 'all'"
          :class="{ selected: itemFilter === 'all' }"
          href="#"
          >All</a
        >
      </li>
      <li>
        <a
          @click="itemFilter = 'active'"
          :class="{ selected: itemFilter === 'active' }"
          href="#"
          >Active</a
        >
      </li>
      <li>
        <a
          @click="itemFilter = 'completed'"
          :class="{ selected: itemFilter === 'completed' }"
          href="#"
          >Completed</a
        >
      </li>
    </ul>
    <button
      @click="clearCompleted"
      v-show="completedItems.length"
      class="clear-completed"
    >
      Clear completed
    </button>
  </footer>
</template>
