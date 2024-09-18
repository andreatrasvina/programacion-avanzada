<template>
  <div>
    <form v-if="!isAuthenticated" @submit.prevent="onSubmit">
      <input required v-model="email" placeholder="Email"/>
      <input required v-model="password" type="password" placeholder="Password"/>
      <button type="submit">Access</button>
    </form>

    <p v-if="message">{{ message }} {{ email }}</p>

    <table v-if="isAuthenticated">
      
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
    };
  },

  created() {
    //verifica si hay usuario enel localStorage
    const storedUser = localStorage.getItem('user');
    if (storedUser) {
      this.isAuthenticated = true;
      this.users = JSON.parse(storedUser); //recupera el usuario
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
          //sessionStorage.setItem('users', JSON.stringify(users));

        } else {
          alert("No existe el usuario");
          this.message = '';
        }
      } catch (error) {
        console.error(error);
      }
    }
  }
};
</script>

<style>

table{
  background-color: pink;
}

th{
  background-color: cornsilk;
}

</style>