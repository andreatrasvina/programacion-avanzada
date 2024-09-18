<template>
  <div>
    <form v-if="!isAuthenticated" @submit.prevent="onSubmit">
      <input required v-model="email" placeholder="Email"/>
      <input required v-model="password" type="password" placeholder="Password"/>
      <button type="submit">Access</button>
    </form>

    <p v-if="message">{{ message }}</p>

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
    };
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