<template>
  <div class="container">
    <h2>Todo</h2>
    <div class="col-lg-5">
      <div class="card">
        <input
          type="text"
          name=""
          placeholder="Todo.."
          aria-label="todo"
          aria-describedby="todo"
          id=""
          v-model="todo_input"
        />

        <button
          class="btn btn-primary"
          v-if="!edit_todo_id"
          @click="saveTodo()"
        >
          Add
        </button>

        <button class="btn btn-primary" v-else @click="updateTodo()">
          Update
        </button>
        <br />

        <button style="width: 25%" class="btn btn-primary" @click="resetTodo()">
          Reset
        </button>

        <table class="table table-bordered">
          <thead>
            <th>S.no</th>
            <th>name</th>
            <th>Action</th>
          </thead>

          <tbody>
            <tr v-for="(todo, index) in todos" :key="index">
              <td>{{ ++index }}</td>

              <td>{{ todo.name }}</td>
              <td>
                <button class="btn btn-info" @click="editTodo(--index)">
                  Edit
                </button>

                <button class="btn btn-danger" @click="deleteTodo(--index)">
                  Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      todos: [],
      api: "http://127.0.0.1:8000/api/todos",
      todo_input: "",
      edit_todo_id: "",
      edit_index: "",
    };
  },

  mounted() {
    this.axios.get(this.api).then((res) => {
      // console.log(res);
      this.todos = res.data;
    });
  },

  methods: {
    saveTodo() {
      if (this.todo_input.length > 0) {
        this.axios.post(this.api, { name: this.todo_input }).then((res) => {
          // console.log(res);
          this.todos.push(res.data);
          this.todo_input = "";
        });
      }
    },

    deleteTodo(index) {
      if (this.todos[index].id) {
        this.axios.delete(this.api + "/" + this.todos[index].id).then((res) => {
          this.todos.splice(index, 1);
        });
      }
    },

    editTodo(index) {
      if (this.todos[index].id) {
        this.todo_input = this.todos[index].name;
        this.edit_todo_id = this.todos[index].id;
        this.edit_index = index;
      }
    },

    updateTodo() {
      if (this.todo_input.length > 0) {
        let data = { name: this.todo_input };
        this.axios
          .put(this.api + "/" + this.todos[this.edit_index].id, data)
          .then((res) => {
            // console.log(res);
            this.todos[this.edit_index].name = res.data.name;
            this.resetTodo();
          });
      }
    },

    resetTodo() {
      this.todo_input = "";
      this.edit_todo_id = "";
      this.edit_index = "";
    },
  },
};
</script>
