<template>
    <div>
      
      <form @submit.prevent="agregarPersona">
        <input v-model="user"  />
        <input v-model="rol" />
        <button type="submit">Save persona</button>
      </form>
  
      <ul>
        <li v-for="persona in personas">
          {{ persona.user }} {{ persona.rol }}
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
  