<template>
  <div>
    <form v-if="!isAuthenticated" @submit.prevent="onSubmit">
      <input required v-model="email" placeholder="Email"/>
      <input required v-model="password" type="password" placeholder="Password"/>
      <button type="submit">Access</button>
    </form>

    <div v-if="isAuthenticated">

      <p v-if="message">{{ message }} {{ email }}</p>
      <button @click="logout">Logout</button>

      <table>
        <thead>
          <tr>
            <th>Email</th>
            <th>Password</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in users" :key="user.email">
            <td>{{ user.email }}</td>
            <td>{{ user.password }}</td>
          </tr>
        </tbody>
      </table>

      <button @click="isVisible=true">Add new user</button>
  
      <form v-if="isVisible" @submit.prevent="addUser">
        <input required v-model="add_email" placeholder="Email"/>
        <input required v-model="add_password" type="password" placeholder="Password"/>
        <button type="submit">Add</button>
        <button @click="isVisible=false" type="button">Cancel</button>
      </form>

    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      email: '',
      password: '',
      message: '',
      isAuthenticated: false,
      users: [],
      isVisible: false,
      add_email: '',
      add_password: '',
    };
  },

  created() {
    const storedUser = localStorage.getItem('user');
    if (storedUser) {
      this.isAuthenticated = true;
      this.users = JSON.parse(storedUser);
      this.message = "SESION GUARDADA!!!";
    }
  },

  methods: {
    async onSubmit() {
      try {
        const response = await fetch('users.json');
        const users = await response.json();

        const validUser = users.some(user =>
          user.email === this.email && user.password === this.password
        );

        if (validUser) {
          this.message = "Bienvenido";
          this.isAuthenticated = true;
          this.users = users;
          localStorage.setItem('user', JSON.stringify(users));
        } else {
          alert("No existe el usuario");
          this.message = '';
        }
      } catch (error) {
        console.error(error);
      }
    },

    logout() {
      localStorage.clear();
      this.isAuthenticated = false;
      this.email = '';
      this.password = '';
    },

    addUser() {
      const newUser = {
        email: this.add_email,
        password: this.add_password
      };

      this.users.push(newUser);
      this.add_email = '';
      this.add_password = ''; 
      this.isVisible = false; 
      this.message = 'Usuario agregado cortectamente'; 

      localStorage.setItem('user', JSON.stringify(this.users));
    }
  }
};
</script>

<style>
table {
  background-color: pink;
}

th {
  background-color: cornsilk;
}
</style>
