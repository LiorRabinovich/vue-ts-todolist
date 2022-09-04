<script setup lang="ts">
import { ref, watch, onMounted } from 'vue';
import type { Ref } from 'vue';
import { v4 as uuidv4 } from 'uuid'

interface TodoItem {
  id: string,
  title: string,
  checked: boolean
}

const input: Ref<string> = ref('');
const todoList: Ref<TodoItem[]> = ref([]);

watch(todoList, (newVal) => {
  localStorage.setItem(
    'todoList',
    JSON.stringify(newVal)
  );
}, {deep: true})

function addItem(): void {
  if (!input.value) {
    alert('Please enter text');
    return;
  }
  todoList.value.push({
    id: uuidv4(),
    title: input.value,
    checked: false,
  })
  input.value = '';
}

function deleteItem(id: string): void {
  todoList.value = todoList.value.filter((item) => item.id !== id);
}

onMounted(() => {
  const todosLS: string | null = localStorage.getItem('todoList');
  todoList.value = todosLS ? JSON.parse(todosLS) : [];
})

</script>

<template>
  <div class="todolist">
    <form @submit.prevent="addItem">
      <input v-model="input" type="text" />
      <button type="submit">ADD</button>
    </form>

    <ul>
      <li v-for="item in todoList" :key="item.id">
        <input type="checkbox" v-model="item.checked" :id="item.id" />
        <label :for="item.id">{{ item.title }}</label>
        <button @click="deleteItem(item.id)">X</button>
      </li>
    </ul>
  </div>
</template>

<style>
  * {
    box-sizing: border-box;
    font-family: 'Roboto', sans-serif;
    margin: 0;
    padding: 0;
    outline: none;
}

body {
    background: #e9f0ea;
    font-size: 100%;
}

.todolist {
    width: 100%;
    max-width: 600px;
    padding: 20px;
    margin: 0 auto;
}

.todolist > form,
.todolist > ul {
    display: flex;
    background: #fff;
    border-radius: 10px;
    padding: 10px;
}

.todolist > form {
    margin-bottom: 20px;
}

.todolist > form input,
.todolist > form button {
    border: 1px solid #4caf50;
    height: 40px;
    line-height: 40px;
    padding: 0;
}

.todolist > form input {
    width: 80%;
    background: #fff;
    border-right: 0;
    border-radius: 10px 0 0 10px;
    padding: 0 10px;
    font-size: 1rem;
}

.todolist > form button {
    width: 20%;
    border-left: 0;
    border-radius: 0 10px 10px 0;
    background: #4caf50;
    color: #fff;
}

.todolist > ul {
    list-style-type: none;
    flex-direction: column;
}

.todolist > ul li {
    display: flex;
    padding: 10px;
    border-radius: 10px;
    background: #eee;
}

.todolist > ul li:not(:last-child) {
    margin-bottom: 10px;
}

.todolist > ul li input {
    margin-right: 10px;
    width: 1rem;
}

.todolist > ul li button {
    margin-left: auto;
    background: #ef5350;
    font-weight: bold;
    color: #fff;
    border: none;
    border-radius: 4px;
    padding: 2px 6px;
}
</style>
