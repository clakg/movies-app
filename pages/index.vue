<template>
  <div>
    <div class="container d-flex align-items-around">
      <div class="home">
        <h1 class="title-custom">
          STEEPLE MOVIES APP
        </h1>

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
        <!-- <div v-else-if="$fetchState.error">
          <div>Error: {{ $fetchState.error.message }}</div>
        </div> -->
        <div v-if="searchInput === ''" class="movies-grid">
          <div v-for="(movie, index) in movies" :key="index" class="flip-card">
            <video-item :movie="movie" />
          </div>
        </div>
        <div v-else class="movies-grid">
          <div v-for="(movie, index) in searchedMovies" :key="index" class="flip-card">
            <video-item :movie="movie" />
          </div>
        </div>
      </div>
      <div class="pagination-custom">
        <button @click="onPreviousPage">
          Page précédente
        </button>
        <button @click="onNextPage">
          Page suivante
        </button>
      </div>
      <!-- <h3 v-if="searchedMovies.length === 0" class="noResult-custom">
        Nous n'avons trouvé aucun résultat à votre rechecher :(
      </h3> -->
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import VideoItem from '~/components/Video-item.vue'

export default {
  name: 'IndexPage',
  components: {
    VideoItem
  },
  data () {
    return {
      movies: [],
      searchedMovies: [],
      searchInput: '',
      loading: false,
      total_pages: 1,
      page_num: 1,
      totalResults: -1
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
  methods: {
    // get movies from api
    async fetchMovies () {
      this.loading = true
      // const data = await axios.get(
      //   'https://api.themoviedb.org/3/movie/now_playing',
      //   {
      //     headers:
      //     {
      //       Authorization: 'bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJiZWUxNjFjM2Q2YTczYzFiYWRmNjRiODAxN2RkODBlNCIsInN1YiI6IjYyZjIyNzIwMTUxMWFhMDA3ZDQyODRjMCIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.KLXn1jHj49w417T36NxE0NENwVQ3Htaqz-5awc_NnkI'
      //     }
      //   }
      // )
      const data = await axios.get('https://api.themoviedb.org/3/movie/now_playing?api_key=030e4ae4fa04b8499f401b541536d268')
      const result = data
      // console.log('XX', result.data.results)
      this.totalResults = result.data.total_results
      result.data.results.forEach((movie) => {
        this.movies.push(movie)
      })
      this.loading = false
    },
    // async getGenres () {
    // const data = await axios.get('https://api.themoviedb.org/3/genre/movie/list?api_key=030e4ae4fa04b8499f401b541536d268')
    // const result = data
    // result.data.results.forEach((movie) => {
    //   this.movies.push(movie)
    // })
    // }

    // get results research from api
    async searchMovies () {
      this.loading = true
      const data = axios
        .get(`https://api.themoviedb.org/3/search/movie?api_key=030e4ae4fa04b8499f401b541536d268&language=en-US&page=${this.page_num}&query=${this.searchInput}`)
      const result = await data
      console.log('XX', result.data.results)
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
.image-custom {
  border-radius: 25px;
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
/* The flip card container - set the width and height to whatever you want. We have added the border property to demonstrate that the flip itself goes out of the box on hover (remove perspective if you don't want the 3D effect */
.flip-card {
  background-color: transparent;
  width: 325px;
  height: 450px;
  border-radius: 25px;
  perspective: 1000px; /* Remove this if you don't want the 3D effect */
}

/* This container is needed to position the front and back side */
.flip-card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  transition: transform 0.8s;
  transform-style: preserve-3d;
}

/* Do an horizontal flip when you move the mouse over the flip box container */
.flip-card:hover .flip-card-inner {
  transform: rotateY(180deg);
}

/* Position the front and back side */
.flip-card-front, .flip-card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  -webkit-backface-visibility: hidden; /* Safari */
  backface-visibility: hidden;
}

/* Style the front side (fallback if image is missing) */
.flip-card-front {
  border-radius: 25px;
  color: black;
}

/* Style the back side */
.flip-card-back {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  padding: 40px 20px;
  background-color: rgb(143, 106, 12);
  border-radius: 25px;
  color: white;
  transform: rotateY(180deg);
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
.pagination-custom{
  @include flexRowCenter;
  margin: 10vh 0;
}
</style>
