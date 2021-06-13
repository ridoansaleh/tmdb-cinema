<template>
  <div class="search-box">
    <h1>Looking for a New Movie?</h1>
    <p>
      Search a movie that you want to see and press the Enter key! All data used
      here are provided by
      <a href="https://www.themoviedb.org/" target="_blank">TMDb</a>.
    </p>
    <input
      v-model="movieTitle"
      class="search-input"
      placeholder="Search a movie"
      @keyup.enter="searchMovie(movieTitle)"
    />
  </div>
  <div v-if="isUserSearching" class="search-information">
    <span>
      {{ displayedMovies.length > 0 ? "Results for" : "No results found for" }}
      <i>"{{ movieTitle }}"</i></span
    >
    <button type="button" class="reset-btn" @click="resetMovies()">
      Reset
    </button>
  </div>
  <div class="movie-container">
    <div
      class="movie-item"
      v-for="movie in displayedMovies"
      v-bind:key="movie.id"
    >
      <img
        :src="'https://www.themoviedb.org/t/p/w185' + movie.poster_path"
        :alt="movie.original_title"
      />
      <h2 class="movie-title">{{ movie.original_title }}</h2>
    </div>
  </div>
  <div
    class="button-container"
    v-if="displayedMovies.length > 0 && !isUserSearching"
  >
    <button type="button" class="load-btn" @click="getMovies((page += 1))">
      Load More
    </button>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";

const BASE_API_URL = "https://api.themoviedb.org/3";
const API_KEY = import.meta.env.VITE_API_KEY;

export default {
  setup() {
    const movieTitle = ref("");
    const storedMovies = ref([]);
    const displayedMovies = ref([]);
    const page = ref(1);
    const isUserSearching = ref(false);

    const toLowerCase = (text) => {
      if (!text) return "";
      return text.toLowerCase();
    };

    const searchMovie = (title) => {
      const search_title = toLowerCase(title);
      displayedMovies.value = storedMovies.value.filter((d) =>
        toLowerCase(d.original_title).includes(search_title)
      );
      isUserSearching.value = true;
    };

    const getMovies = (pn = 1) => {
      fetch(`${BASE_API_URL}/movie/now_playing?api_key=${API_KEY}&page=${pn}`)
        .then((response) => response.json())
        .then((data) => {
          storedMovies.value = [...storedMovies.value, ...data.results];
          displayedMovies.value = storedMovies.value;
        })
        .catch(console.log);
    };

    const resetMovies = () => {
      isUserSearching.value = false;
      movieTitle.value = "";
      displayedMovies.value = storedMovies.value;
    };

    onMounted(() => {
      getMovies();
    });

    return {
      movieTitle,
      page,
      displayedMovies,
      isUserSearching,
      searchMovie,
      getMovies,
      resetMovies,
    };
  },
};
</script>

<style>
body {
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.search-box {
  margin-bottom: 25px;
  text-align: left;
  background-color: #58d68d;
  padding: 20px;
  border-bottom: 6px solid #ebcc6c;
  color: #fff;
}

.search-input {
  width: 100%;
  padding: 10px;
  border: 0;
  box-sizing: border-box;
}

.search-information {
  display: flex;
  justify-content: space-between;
  flex-direction: column-reverse;
  font-weight: bold;
  margin: 0 20px 20px;
}

.reset-btn {
  background-color: #eb816c;
  border: 0;
  color: #fff;
  cursor: pointer;
  padding: 10px 40px;
  margin-bottom: 10px;
}

.movie-container {
  display: flex;
  flex-wrap: wrap;
  margin: 0 20px 20px;
}

.movie-item {
  width: 195px;
  margin-right: 15px;
  margin-bottom: 15px;
}

.movie-title {
  font-size: 18px;
  text-align: right;
  padding: 0 5px;
}

.load-btn {
  background-color: #eb816c;
  border: 0;
  color: #fff;
  margin-top: 10px;
  padding: 10px 45px;
  cursor: pointer;
  font-weight: bold;
  font-size: 24px;
}

.button-container {
  margin-bottom: 20px;
}

@media screen and (min-width: 768px) {
  .search-information {
    flex-direction: row;
  }

  .reset-btn {
    margin-bottom: 0;
  }
}

@media screen and (min-width: 1024px) {
  .search-box {
    margin-bottom: 45px;
  }

  .search-input {
    width: 320px;
  }
}
</style>
