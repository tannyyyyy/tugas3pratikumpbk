<template>
  <div>
    <header>
      <center style="color: bisque;">
        <h1>Tanny Apriliani</h1>
      </center>
      <center>
        <button class="btn btn-link" @click="showSection('todos')">
          <h2 style="color: bisque;">ToDos</h2>
        </button>
        <button class="btn btn-link" @click="showSection('posts')">
          <h2 style="color: bisque;">Posts</h2>
        </button>
      </center>
    </header>

    <div v-if="currentSection === 'todos'">
      <center><h2 style="color: bisque;">ToDo List Keranjang Belanja</h2></center>
      <div class="container-fluid" style="width: 40em">
        <div class="card m-3">
          <div class="card-header">
            <form @submit.prevent="addTodo">
              <div class="col-12">
                <div class="row">
                  <div class="col-3">
                    <label for="newTodo" class="form-label">Ingin dibeli</label>
                  </div>
                  <div class="col-7">
                    <input v-model="newTodo" type="text" class="form-control form-control-sm" name="newTodo" id="newTodo">
                  </div>
                  <div class="col-2">
                    <button class="btn btn-primary btn-sm float-end" type="submit" name="button">
                      <i class="bi bi-plus"></i>
                    </button>
                  </div>
                </div>
              </div>
            </form>
          </div>
          <div class="card-body">
            <div class="float-end">
              <button class="btn btn-success btn-sm me-1" @click="allDone" type="button" name="button">
                <i class="bi bi-check-all"></i>
              </button>
              <button class="btn btn-danger btn-sm me-1" @click="allRemove" type="button" name="button">
                <i class="bi bi-trash-fill"></i>
              </button>
              <!-- New Buttons for Filter -->
              <button class="btn btn-warning btn-sm me-1" @click="toggleFilter('done')" type="button" name="button">
                Selesai
              </button>
              <button class="btn btn-warning btn-sm me-1" @click="toggleFilter('undone')" type="button" name="button">
                Belum
              </button>
            </div>
          </div>
          <ul class="list-group list-group-flush list-group-numbered">
            <li v-for="todo in filteredTodos" :key="todo.title" :class="{ 'list-group-item-secondary': todo.done }" class="list-group-item">
              <div class="form-check">
                <input class="form-check-input" type="checkbox" v-model="todo.done" :id="'checkbox-' + todo.title">
                <label class="form-check-label ms-3" :for="'checkbox-' + todo.title" :style="{ 'text-decoration': todo.done ? 'line-through' : 'none' }">
                  {{ todo.title }}
                </label>
              </div>
              <button @click="removeTodo(todo)" class="btn btn-danger btn-sm float-end" type="button" name="button">
                <i class="bi bi-trash-fill"></i>
              </button>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <div v-if="currentSection === 'posts'">
      <div class="container-fluid" style="width: 40em">
        <div class="card m-3">
          <div class="card-header">
            <h2>Posts</h2>
          </div>
          <div class="card-body">
            <div class="mb-3">
              <label for="userSelect" class="form-label">Filter by User</label>
              <select v-model="selectedUser" @change="fetchPosts" class="form-select" id="userSelect">
                <option value="">Select a user</option>
                <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
              </select>
            </div>
            <ul v-if="posts.length" class="list-group list-group-flush">
              <li v-for="post in filteredPosts" :key="post.id" class="list-group-item">
                <h5>{{ post.title }}</h5>
                <p>{{ post.body }}</p>
                <small>User: {{ getUser(post.userId) }}</small>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ToDoList',

  data: () => ({
    newTodo: '',
    todos: [],
    filter: null,
    posts: [],
    users: [],
    selectedUser: '',
    currentSection: 'todos' // Default section
  }),

  methods: {
    addTodo() {
      this.todos.push({
        title: this.newTodo,
        done: false
      });
      this.newTodo = '';
    },
    removeTodo(todo) {
      const todoIndex = this.todos.indexOf(todo);
      this.todos.splice(todoIndex, 1);
    },
    allDone() {
      this.todos.forEach(todo => {
        todo.done = true;
      });
    },
    allRemove() {
      this.todos = [];
    },
    toggleFilter(type) {
      if (this.filter === type) {
        this.filter = null;
      } else {
        this.filter = type;
      }
    },
    async fetchPosts() {
      try {
        const usersResponse = await fetch('https://jsonplaceholder.typicode.com/users');
        this.users = await usersResponse.json();
        if (this.selectedUser) {
          const postsResponse = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUser}`);
          this.posts = await postsResponse.json();
        } else {
          this.posts = [];
        }
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    getUser(userId) {
      const user = this.users.find(user => user.id === userId);
      return user ? user.name : 'Unknown User';
    },
    showSection(section) {
      this.currentSection = section;
    }
  },

  computed: {
    filteredTodos() {
      if (this.filter === 'done') {
        return this.todos.filter(todo => todo.done);
      } else if (this.filter === 'undone') {
        return this.todos.filter(todo => !todo.done);
      } else {
        return this.todos;
      }
    },
    filteredPosts() {
      return this.posts.filter(post => !this.selectedUser || post.userId === parseInt(this.selectedUser));
    }
  },

  mounted() {
    this.fetchPosts();
  }
}
</script>

<style scoped>
.list-group-item-secondary .form-check-label {
  color: #808080; /* Warna teks abu-abu */
}
</style>


<style>
body{
  background-image:url(./assets/cb.jpeg);
background: cover;
background-size: cover;
background-repeat: no-repeat;
}
</style>