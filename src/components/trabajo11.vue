<template>
  <div>
    <form v-if="!isAuthenticated" @submit.prevent="onSubmit">
      <input required v-model="email" placeholder="Email"/>
      <input required v-model="password" type="Password" placeholder="Password"/>
      <button type="submit">Access</button>
    </form>

    <div v-if="isAuthenticated">
      <p v-if="message">{{ message }}</p>
      <button @click="logout">Logout</button>

      <div v-if="!selectedMovie" class="carousel">
        <div class="carousel-track" :style="{ transform: `translateX(-${currentIndex * 100}%)` }">
          <div v-for="movie in movies" :key="movie.id" class="carousel-item">
            <img :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`" :alt="movie.title"/>
            <h3>{{ movie.title }}</h3>
            <button @click="viewMore(movie)">Ver más</button>
          </div>
        </div>
      </div>

      <div v-if="selectedMovie">
        <img :src="`https://image.tmdb.org/t/p/w500${selectedMovie.poster_path}`"/>
        <h2>{{ selectedMovie.title }}</h2>
        <p>{{ selectedMovie.overview }}</p>

        <input v-model="rating" type="number" min="1" max="10" placeholder="(1-10)"/>
        <button @click="submitRating">Enviar Calificación</button>
        <button @click="removeRating">Eliminar Calificación</button>
        <button @click="selectedMovie = null">Regresar</button>
      </div>

    </div>
  </div>
</template>

<script>
export default{
  data() {
    return {
      email: '',
      password: '',
      message: '',
      isAuthenticated: false,
      movies:[],
      currentIndex: 0,
      selectedMovie: null,
      rating: '',
    };
  },

  created(){
    const storedUser = localStorage.getItem('user');
    if (storedUser){
      this.isAuthenticated = true;
      this.message = "Sesion recuperada";
      this.fetchMovies();
      this.startCarousel();
    }
  },

  methods:{
    async onSubmit(){
      try {
        const myHeaders = new Headers();
        myHeaders.append("Content-Type", "application/json");
        
        const raw = JSON.stringify({
          username: this.email,
          password: this.password,
          request_token: ""
        });

        const requestOptions = {
          method: "POST",
          headers: myHeaders,
          body: raw,
          redirect: "follow"
        };

        const loginResponse = await fetch("https://api.themoviedb.org/3/authentication/token/validate_with_login?api_key=450ea0f4ed5fe9c75ca3a400d9f761f9", requestOptions);
        const loginData = await loginResponse.json();

        if (!loginResponse.ok || !loginData.success) {
          throw new Error('Este usuario no existe');
        }

        this.isAuthenticated = true;
        this.message = `Bienvenido, ${this.email}`;
        localStorage.setItem('user', this.email);
        
        this.fetchMovies();
        this.startCarousel();

      } catch (error){
        alert(error.message);
      }
    },

    async fetchMovies(){
      try {
        const apiKey = '450ea0f4ed5fe9c75ca3a400d9f761f9';
        const response = await fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}&page=1`);
        const data = await response.json();
        this.movies = data.results;
      } catch (error) {
        console.error('Error');
      }
    },

    startCarousel(){
      setInterval(() => {
        this.currentIndex = (this.currentIndex + 1) % 5; //5 para reiniciar desp de todo
      }, 5000); 
    },

    viewMore(movie) {
      this.selectedMovie = movie;
      this.rating = '';
    },

    async submitRating() {
      try {
        const movieId = this.selectedMovie.id;

        if (!this.rating || this.rating < 1 || this.rating > 10) {
          alert('Ingrese calificaicon entre 1 y 10');
          return;
        }

        const options = {
          method: 'POST',
          headers: {
            accept: 'application/json',
            'Content-Type': 'application/json;charset=utf-8',
            Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiI0NTBlYTBmNGVkNWZlOWM3NWNhM2E0MDBkOWY3NjFmOSIsIm5iZiI6MTcyNzI0MDc5Mi4wNzk0NTMsInN1YiI6IjY2ZjJmNjgxYTgyYjAwNTcwMzI2ZmY3YyIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.9vpuVdynbcWWiHFQniA_ZX_iTLxdAuV-X_RwjH-5Pb8'
          },
          body: JSON.stringify({ value: this.rating })
        };

        const response = await fetch(`https://api.themoviedb.org/3/movie/${movieId}/rating`, options);

        if (response.ok) {
          alert('Enviada');
        } else {
          throw new Error('No se envio');
        }
      } catch (error) {
        console.error('Error:', error);
        alert(error.message);
      }
    },

    async removeRating() {
      try {
        const movieId = this.selectedMovie.id;

        const options = {
          method: 'DELETE',
          headers: {
            accept: 'application/json',
            'Content-Type': 'application/json;charset=utf-8',
            Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiI0NTBlYTBmNGVkNWZlOWM3NWNhM2E0MDBkOWY3NjFmOSIsIm5iZiI6MTcyNzI0MDc5Mi4wNzk0NTMsInN1YiI6IjY2ZjJmNjgxYTgyYjAwNTcwMzI2ZmY3YyIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.9vpuVdynbcWWiHFQniA_ZX_iTLxdAuV-X_RwjH-5Pb8'
          }
        };

        const response = await fetch(`https://api.themoviedb.org/3/movie/${movieId}/rating`, options);

        if (response.ok) {
          alert('Eliminado');
          this.rating = '';
        } else {
          throw new Error('Mo se elimino');
        }
      } catch (error) {
        console.error('Error:', error);
        alert(error.message);
      }
    },

    logout(){
      localStorage.clear();
      this.isAuthenticated = false;
      this.email = '';
      this.password = '';
      this.movies = [];
      this.selectedMovie = null;
      this.rating = '';
    },
  },
};
</script>

<style>
.carousel{
  overflow: hidden;
  width: 100%;
}

.carousel-track{
  display: flex;
  transition: transform 0.5s ease-in-out; /*transición*/
}

.carousel-item{
  min-width: 25%; /*4*/
  text-align: center;
}

.carousel-item img{
  max-width: 100%;
}

button{
  background-color: #000000;
  color: white;
  border-radius: 5px;
  cursor: pointer;
}

button:hover{
  background-color: #ffffff;
  color: rgb(0, 0, 0);
}

</style>

