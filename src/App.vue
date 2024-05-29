<script>
import todos from "./data/todos";
import StatusFilter from './components/StatusFilter.vue';

export default {
  components: {
    StatusFilter,
  },
  data() {
    let todos = [];
    const jsonData = localStorage.getItem('todos') || [];

    try {
      todos = JSON.parse(jsonData);
    } catch (e) {}

    return {
      todos,
      title: '',
      status: 'all',
    };
  },
  watch: {
    todos: {
      deep: true,
      handler() {
        localStorage.setItem('todos', JSON.stringify(this.todos));
    }
    }
  },
  computed: {
    activeTodos() {
      return this.todos.filter(todo => !todo.completed)
    },
  },
  methods: {
    handleSubmit() {
      this.todos.push({
        id: Date.now(),
        title: this.title,
        completed: false
      });

      this.title = '';
    }
  }
};
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <button
          data-cy="ToggleAllButton"
          type="button"
          class="todoapp__toggle-all"
          :class="{ active: activeTodos.length === 0 }"
        />

        <form @submit.prevent="handleSubmit">
          <input
            data-cy="NewTodoField"
            type="text"
            ref="{newTodoField}"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            v-model="title"
          />
        </form>
      </header>

      <section class="todoapp__main" data-cy="TodoList">
        <div
          v-for="todo, index of todos"
          :key="todo.id"
          class="todo"
          :class="{ completed: todo.completed }"
        >
          <label class="todo__status-label">
            <input
              data-cy="TodoStatus"
              type="checkbox"
              class="todo__status"
              v-model="todo.completed"
              
            />
          </label>

          <form v-if="false">
            <input
              data-cy="TodoTitleField"
              type="text"
              class="todo__title-field"
              placeholder="Empty todo will be deleted"
              defaultValue="JS"
            />
          </form>
          <template v-else>
            <span data-cy="TodoTitle" class="todo__title" >{{
              todo.title
            }}</span>
            <button
              type="button"
              class="todo__remove"
              data-cy="TodoDeleteButton"
              v-on:click="todos.splice(index, 1)"
            >
              ×
            </button>
          </template>

          <div
            data-cy="TodoLoader"
            class="modal overlay"
            :class="{ 'is-active': false }"
          >
            <div class="modal-background has-background-white-ter" />
            <div class="loader" />
          </div>
        </div>
      </section>

      <footer class="todoapp__footer" data-cy="Footer">
        <span class="todo-count" data-cy="todosCounter"> {{ activeTodos.length }} items left </span>

        <StatusFilter
          v-model="status"
        />

        <button
          data-cy="ClearCompletedButton"
          type="button"
          class="todoapp__clear-completed"
          v-if="activeTodos.length > 0"
        >
          Clear completed
        </button>
      </footer>
    </div>

    <!-- <div data-cy="ErrorNotification" class="notification is-danger is-light has-text-weight-normal">
      <button data-cy="HideErrorButton" type="button" class="delete" />

      Unable to add a todo
      <br />
      Unable to delete a todo
      <br />
      Unable to update a todo
    </div> -->
  </div>
</template>
