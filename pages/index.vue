<template>
  <div>
    <div class="container d-flex align-items-around">
      <div class="home">
        <div class="title-custom">
          <h1>
            MOVIES APP
          </h1>
          <img src="../assets/imgs/logo.png" alt="" class="logo-custom">
        </div>
        <!-- Search -->
        <div class="container search">
          <input
            v-model.lazy="searchInput"
            type="text"
            placeholder="Commencer votre recherche et appuyer sur entrée"
            @keyup.enter="$fetch"
          >
          <button v-show="searchInput !== ''" class="button" @click="resetSearch">
            X
          </button>
          <p class="results-custom">
            {{ searchInput === '' ? resultMoviesCount : resultSearchCount }}
          </p>
        </div>
        <div v-if="$fetchState.pending" class="load">
          <span />
        </div>
        <div v-if="searchInput === ''" class="movies-grid">
          <div v-for="(movie, index) in movies" :key="index" class="flip-card">
            <movie-item :movie="movie" :genres="genres" @action-in-parent="actionInParent()" />
          </div>
        </div>
        <div v-else class="movies-grid">
          <div v-for="(movie, index) in searchedMovies" :key="index" class="flip-card">
            <movie-item :movie="movie" />
          </div>
        </div>
      </div>
      <div>
        <button v-if="currentPage > 1" @click="currentPage--;fetchMovies()">
          Page précédente
        </button>
        <button v-if="currentPage < totalPages" @click="currentPage++;fetchMovies()">
          Page suivante
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import MovieItem from '../components/MovieItem.vue'

export default {
  name: 'IndexPage',
  components: {
    MovieItem
  },
  data () {
    return {
      movies: [],
      currentPage: 1,
      totalPages: null,
      filmsPerPage: 20,
      searchedMovies: [],
      searchInput: '',
      loading: false,
      // total_pages: 1,
      // page_num: 1,
      // totalResults: -1,
      genres: []
    }
  },
  async fetch () {
    this.searchInput === '' ? await this.fetchMovies() : await this.searchMovies()
  },
  fetchDelay: 2500,
  computed: {
    resultSearchCount () {
      if (this.searchInput !== '') {
        return `${this.totalResults} résultats`
      }
      return 'Aucun résultat'
    },
    resultMoviesCount () {
      return this.searchInput === '' ? `${this.totalResults} résultats` : 'Aucun résultat'
    }
  },
  watch: {
    searchInput () {
      console.log(this.searchInput)
    }
  },
  mounted () {
    this.fetchMovies()
  },
  methods: {
    // get genres
    async getGenres () {
      const resultTypes = await axios.get('https://api.themoviedb.org/3/genre/movie/list?api_key=030e4ae4fa04b8499f401b541536d268')
      this.genres = resultTypes.data.genres.map(g => g)
    },
    // get movies from api
    async fetchMovies () {
      this.loading = true
      this.getGenres()
      const data = await axios.get(`https://api.themoviedb.org/3/movie/now_playing?api_key=030e4ae4fa04b8499f401b541536d268&page=${this.currentPage}`)
      const result = data
      this.movies = result.data.results
      this.totalPages = result.data.total_pages
      result.data.results.forEach((movie) => {
        this.movies.push(movie)
      })
      this.loading = false
    },
    // get results research from api
    async searchMovies () {
      this.loading = true
      const data = axios
        .get(`https://api.themoviedb.org/3/search/movie?api_key=030e4ae4fa04b8499f401b541536d268&language=en-US&page=${this.page_num}&query=${this.searchInput}`)
      const result = await data
      this.totalResults = result.data.total_results
      result.data.results.forEach((movie) => {
        this.searchedMovies.push(movie)
      })
      this.loading = false
    },

    // initialize searchbar
    resetSearch () {
      this.searchInput = ''
      this.searchedMovies = []
      this.fetchMovies()
    },

    // Pagination
    onNextPage () {
      if (this.movies) {
        this.page_num += 1
        this.fetchMovies()
      }
    },
    onPreviousPage () {
      if (this.movies) {
        this.page_num -= 1
        this.fetchMovies()
      }
    },
    actionInParent () {
      // have to install vue router to redirect to one movie page
      console.log('DISPLAY IN PARENT FROM CHILD')
    }
  }
}
</script>

<style lang="scss" scoped>

@mixin flexRowCenter {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
.home {
    .search {
      display: flex;
      padding: 32px 16px;
      input {
        max-width: 500px;
        width: 100%;
        padding: 12px 10px;
        font-size: 14px;
        border: none;
        &:focus {
          outline: none;
        }
      }
    .button {
      width: 50px;
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
      border-top-right-radius: 25px;
      border-bottom-right-radius: 25px;
      border-top-color:lightgray;
      border-bottom-color: lightgrey;
    }
  }
}
.title-custom {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 5%;
}

.movies-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(4, 1fr);
  grid-column-gap: 10px;
  grid-row-gap: 20px;
  @media (min-width: 500px) {
    grid-template-columns: repeat(1, 1fr);
  }
  @media (min-width: 750px) {
    grid-template-columns: repeat(3, 1fr);
  }
  @media (min-width: 1100px) {
    grid-template-columns: repeat(4, 1fr);
  }
}
/* The flip card container - set the width and height to whatever you want. We have added the border property to demonstrate that the flip itself goes out of the box on hover (remove perspective if don't want the 3D effect */
.flip-card {
  background-color: transparent;
  width: 325px;
  height: 450px;
  border-radius: 25px;
  perspective: 1000px; /* Remove this if don't want the 3D effect */
}

/* style loader */
.loading {
  height: 100vh;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;

  @keyframes circle-loading {
      to {
          transform: rotateZ('360deg');
      }
  }
  span {
      display: block;
      width: 300px;
      height: 300px;
      border-radius: 50%;
      border: 2px solid transparent;
      border-top-color: rgb(213, 135, 25);
      animation: circle-loading 5000ms ease infinite;
  }
}

@keyframes rotate {
  from {transform: rotate(0deg)}
  to {transform: rotate(360deg)}
}

@-webkit-keyframes rotate {
  from {-webkit-transform: rotate(0deg)}
  to {-webkit-transform: rotate(360deg)}
}
.load {
  width: 100px;
  height: 100px;
  margin: 110px auto 0;
  border:solid 10px #e2a711;
  border-radius: 50%;
  border-right-color: transparent;
  border-bottom-color: transparent;
  -webkit-transition: all 0.5s ease-in;
  -webkit-animation-name: rotate;
  -webkit-animation-duration: 2.0s;
  -webkit-animation-iteration-count: infinite;
  -webkit-animation-timing-function: linear;
  transition: all 0.5s ease-in;
  animation-name:rotate;
  animation-duration: 2.0s;
  animation-iteration-count: infinite;
  animation-timing-function: linear;
}
.results-custom {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin: 1%;
}
.noResult-custom {
  @include flexRowCenter()
}
.pagination-custom {
  @include flexRowCenter;
  margin: 10vh 0;
}
.logo-custom {
  mix-blend-mode: multiply;
  height: 100px;
  width: 100px;
  margin-left: 50px;
}
</style>
