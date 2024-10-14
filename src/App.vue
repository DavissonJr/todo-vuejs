<template>
  
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        Olá, <input type="text" placeholder="Insira o nome aqui" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CRIAR TAREFA</h3>

      <form @submit.prevent="addTodo">
          <h4>Qual é sua tarefa?</h4>
          <input type="text" placeholder="Tarefa" v-model="input_content" />

        <h4>Escolha uma categoria</h4>
        <div class="options">

          <label>
            <input type="radio" name="category" value="business" v-model="input_category"/>
            <span class="bubble business"></span>
            <div>Trabalho</div>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Pessoal</div>
          </label>

        </div>

        <input type="submit" value="Adicionar TODO">
      </form>
    </section>

    <section class="todo-list">
  <h3>TAREFAS</h3>
  <div class="list" id="todo-list">
    <div v-for="todo in todos_asc" :key="todo.createdAt" :class="['todo-item', { done: todo.done }]">

      <label>
        <input type="checkbox" v-model="todo.done">
        <span :class="['bubble', todo.category]"></span>
      </label>

      <div class="todo-content">
        <input type="text" v-model="todo.content">
      </div>

      <div class="actions">
        <button class="delete" @click="removeTodo(todo)">Excluir</button>
      </div>

    </div>
  </div>
</section>
    
  </main>

</template>

<script>
import { ref, onMounted, computed, watch } from 'vue';

export default {
  setup() {
    const todos = ref([]);
    const name = ref('');

    const input_content = ref('');
    const input_category = ref(null);

    const todos_asc = computed(() => todos.value.toSorted((a, b) => {
      return b.createdAt - a.createdAt;
    }));

    const addTodo = () => {
      if (input_content.value.trim() === '' || input_category.value === null) {
        return;
      }

      todos.value.push({
        content: input_content.value,
        category: input_category.value,
        done: false,
        createdAt: new Date().getTime()
      });

      input_content.value = '';
      input_category.value = null;
    };

    const removeTodo = (todo) => {
      todos.value = todos.value.filter(t => t !== todo);
    };

    watch(todos, (newVal) => {
      localStorage.setItem('todos', JSON.stringify(newVal));
    }, { deep: true });

    watch(name, (newVal) => {
      localStorage.setItem('name', newVal);
    });

    onMounted(() => {
      name.value = localStorage.getItem('name') || '';
      todos.value = JSON.parse(localStorage.getItem('todos')) || [];
    });


    return {
      todos,
      name,
      input_content,
      input_category,
      todos_asc,
      addTodo,
      removeTodo,
    };
  }
};
</script>

<style>

</style>
