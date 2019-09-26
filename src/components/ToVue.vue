<template>
  <div>
    <section class="section">
      <div class="columns is-centered">
        <div class="column is-half">
          <form v-on:submit.prevent="addTask">
            <h2 class="subtitle is-3">Add task</h2>
            <div class="field">
              <label for="description" class="label">Description</label>
              <div class="control">
                <input id="description" type="text" class="input" v-model="description" />
              </div>
            </div>

            <div class="field">
              <label for="status" class="label">Status</label>
              <div class="control">
                <div class="select">
                  <select v-model="status" id="status">
                    <option value="0">To do</option>
                    <option value="1">Done</option>
                  </select>
                </div>
              </div>
            </div>

            <div class="field is-grouped">
              <div class="control">
                <button class="button is-link">Submit</button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </section>

    <section class="section has-background-white-ter">
      <div class="container">
        <div class="columns is-centered">
          <div class="column">
            <h2 class="title is-2">Todo</h2>
            <ul class="todo">
              <li class="card" v-for="task in todos" :key="task.id">
                <div class="card-content">
                  <div class="content">{{task.description}}</div>
                </div>
                <footer class="card-footer">
                  <a @click="setStatus(task.id)" class="card-footer-item">Done</a>
                </footer>
              </li>
            </ul>
          </div>

          <div class="column">
            <h2 class="title is-2">Done</h2>
            <ul class="todo">
              <li class="card" v-for="task in dones" :key="task.id">
                <div class="card-content done">
                  <div class="content">{{task.description}}</div>
                </div>
                <footer class="card-footer">
                  <a @click="deleteTask(task.id)" class="card-footer-item red">Delete</a>
                </footer>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";

const url = "https://dys-django-rest-todo.herokuapp.com/tasks/";
const auth = {
  username: process.env.VUE_APP_USER,
  password: process.env.VUE_APP_PW
};

export default {
  name: "ToVue",
  data() {
    return {
      tasks: [],
      description: "",
      status: 0
    };
  },
  computed: {
    todos() {
      return this.tasks.filter(task => task.status === 0);
    },
    dones() {
      return this.tasks.filter(task => task.status === 1);
    }
  },
  mounted() {
    this.getTasks();
  },
  methods: {
    getTasks() {
      axios({
        method: "get",
        url: url,
        auth: auth
      }).then(response => (this.tasks = response.data));
    },
    addTask() {
      if (this.description) {
        axios({
          method: "post",
          url: url,
          data: {
            description: this.description,
            status: this.status
          },
          auth: auth
        })
          .then(response => {
            let newTask = {
              id: response.data.id,
              description: this.description,
              status: parseInt(this.status)
            };
            this.tasks.push(newTask);
            this.description = "";
            this.status = 0;
          })
          .catch(error => console.log(error));
      }
    },
    setStatus(task_id) {
      let description;
      for (let i = 0; i < this.tasks.length; i++) {
        if (this.tasks[i].id === task_id) {
          this.tasks[i].status = 1;
          description = this.tasks[i].description;
          break;
        }
      }
      axios({
        method: "put",
        url: `${url}${task_id}/`,
        headers: { "Content-Type": "application/json" },
        data: {
          description: description,
          status: 1
        },
        auth: auth
      });
    },
    deleteTask(task_id) {
      for (let i = 0; i < this.tasks.length; i++) {
        if (this.tasks[i].id === task_id) {
          this.tasks[i].status = null;
          break;
        }
      }
      axios({
        method: "delete",
        url: `${url}${task_id}/`,
        auth: auth
      });
    }
  }
};
</script>

<style scoped>
.red {
  color: red;
}
.card {
  margin-bottom: 25px;
}
.done {
  opacity: 0.5;
}
</style>
