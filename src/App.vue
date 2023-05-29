<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  } else {
    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      createdAt: new Date().getTime(),
    });
    input_content.value = "";
    input_category.value = null;
  }
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
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

<script>
export default {
  data() {
    return {
      show: false,
    };
  },
  methods: {
    toggleAnimation() {
      this.show = !this.show;
    },
  },
};
</script>

<template>
  <!-- Saludo -->
  <main>
    <div class="container">
      <div class="row">
        <div class="cols">
          <section class="mt-3 text-black">
            <h2 class="d-flex align-items-center">
              Bienvenido,
              <input
                class="input-name ms-1 bg-transparent"
                type="text"
                v-model="name"
              />
            </h2>
          </section>
        </div>
      </div>
      <!--  Form crear tarea -->
      <div class="row">
        <div class="cols">
          <section class="create-todo">
            <h3 class="font-monospace fs-5 text-secondary">CREAR UNA TAREA</h3>
            <form
              @submit.prevent="addTodo"
              class="border rounded p-3 bg-light shadow"
            >
              <h4 class="mb-2">¿Qué agregarás hoy?</h4>
              <input
                type="text"
                class="form-control"
                placeholder="ej. Estudiar Vue"
                v-model="input_content"
              />
              <h4 class="mt-3 mb-2">Elige una categoría</h4>
              <div class="form-check">
                <input
                  type="radio"
                  class="form-check-input"
                  name="category"
                  value="trabajo"
                  v-model="input_category"
                />
                <label class="form-check-label">
                  <div>Trabajo</div>
                </label>
              </div>
              <div class="form-check">
                <input
                  type="radio"
                  class="form-check-input"
                  name="category"
                  value="personal"
                  v-model="input_category"
                />
                <label class="form-check-label">
                  <div>Personal</div>
                </label>
              </div>

              <input
                class="mt-3 btn btn-dark"
                type="submit"
                value="Agregar tarea"
              />
            </form>
          </section>
        </div>
      </div>
      <!-- Listado de tareas -->
      <section class="todo-list mt-3">
        <h3>Lista de tareas</h3>
        <div class="list">
          <TransitionGroup name="list" tag="div" class="row">
              <div
                class="col-xs-12 col-sm-12 col-lg-4 mb-3"
                v-for="todo in todos_asc"
                :class="`todo-item ${todo.done && 'done'}`"
              >
                <div
                  class="card shadow"
                  :class="`${todo.done && 'border-success'}`"
                >
                  <div class="card-body">
                    <label>
                      <input
                        class="form-check-input"
                        type="checkbox"
                        v-model="todo.done"
                      />
                      <span class="ms-2">¿Tarea realizada?</span>
                    </label>

                    <div class="todo-content">
                      <input
                        class="input-task"
                        type="text"
                        v-model="todo.content"
                      />
                    </div>

                    <div
                      class="actions d-flex align-items-center justify-content-between"
                    >
                      <button
                        class="mt-3 btn btn-outline-danger"
                        @click="removeTodo(todo)"
                      >
                        Delete
                      </button>
                      <span class="mt-3">{{
                        todo.category.charAt(0).toUpperCase() +
                        todo.category.slice(1)
                      }}</span>
                    </div>
                  </div>
                </div>
              </div>
          </TransitionGroup>
        </div>
      </section>
    </div>
  </main>
</template>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500&display=swap");

main {
  min-height: 100vh;
  background-color: #eaf5ff;
  font-family: "Montserrat", sans-serif;
}

span {
  font-weight: 300;
}

h2 {
  font-weight: 500;
}

h3,
h4 {
  font-weight: 400;
}
.input-name {
  border: none;
  font-weight: 500;
  transition: 0.1s;
  width: 40%;
}

.input-name:focus {
  outline: none;
}

.input-task {
  border: none;
  font-weight: 500;
  transition: 0.1s;
  width: 95%;
}

.input-task:focus {
  outline: none;
}

.todo-item.done .todo-content input {
  text-decoration: line-through;
  color: var(--grey);
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
