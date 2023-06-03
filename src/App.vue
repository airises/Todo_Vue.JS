<script setup>
  import { ref, onMounted, computed, watch } from "vue";

  const name = ref("");
  const todos = ref([]);

  const input_content = ref("");
  const input_category = ref(null);

  const todos_action = computed(() =>
    todos.value.sort((a, b) => {
      return b.createdAt - a.createdAt;
    })
  );

  const removeTodo = (todo) => {
    todos.value = todos.value.filter((el) => el !== todo);
  };

  const addTodo = () => {
    if (input_content.value.trim() === "" || input_category.value === null) {
      return;
    }
    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      createdAt: new Date().getTime(),
    });

    input_content.value = "";
    input_category.value = null;
  };

  watch(
    todos,
    (newVal) => {
      localStorage.setItem("todos", JSON.stringify(newVal));
    },
    { deep: true }
  );

  watch(name, (newVal) => {
    localStorage.setItem("name", newVal);
  });

  onMounted(() => {
    name.value = localStorage.getItem("name") || "";
    todos.value = JSON.parse(localStorage.getItem("todos")) || [];
  });
</script>

<template>
  <main className="app">
    <section className="greeting">
      <h1 className="title">
        What's up,
        <input
          className="form-input"
          type="text"
          placeholder="Your Name "
          v-model="name"
        />
      </h1>
    </section>
    <section class="created-todo">
      <h3>Create a todo :</h3>
      <form @submit.prevent="addTodo">
        <h5>What's on your todo list?</h5>
        <input
          className="input"
          type="text"
          placeholder="write todo list"
          v-model="input_content"
        />

        <h4>Pick a category :</h4>

        <div class="options">
          <div class="business">
            <label>
              <input
                type="radio"
                name="category"
                value="business"
                v-model="input_category"
              />
              <p>Business</p>
            </label>
          </div>

          <div class="personal">
            <label>
              <input
                type="radio"
                name="category"
                value="personal"
                v-model="input_category"
              />
              <p>Personal</p>
            </label>
          </div>
        </div>

        <button class="submit" type="submit">Add todo</button>
      </form>
    </section>

    <section class="todo-list">
      <h2>TODO LIST :</h2>
      <div class="list">
        <div
          v-for="todo in todos_action"
          :class="` todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
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

<style scoped></style>
