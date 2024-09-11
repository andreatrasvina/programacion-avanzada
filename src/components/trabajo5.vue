<template>

  <div class="app-container">
    <form @submit.prevent="agregarPersona" class="form">
        <input v-model="user"  />
        <input v-model="rol" />
        <button type="submit">Save</button>
    </form>

    <ul>
      <li v-for="persona in personas" class="persona-item">
        {{ persona.user }} : {{ persona.rol }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: '',
      rol: '',
      personas: []
    };
  },

  methods: {
      agregarPersona() { 

        this.personas.push({ user: this.user, rol: this.rol });
        
      }
  }
};
</script>

<style>
.app-container {
  max-width: 600px;
  margin: auto;
  padding: 20px;
  font-family: Arial, sans-serif;
  background-color: #ddd;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 20px;
}

.persona-item {
  padding: 10px;
  border-bottom: 1px solid #ddd;
  list-style: none;
}
</style>
