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
            placeholder="Search"
            @keyup.enter="$fetch"
          >
          <button v-show="searchInput !== ''" class="button" @click="resetSearch">
            X
          </button>
        </div>
        <div v-if="$fetchState.pending" class="load">
          <span />
          <br>
          <span />
        </div>
        <div v-else-if="$fetchState.error">
          <div>Error: {{ $fetchState.error.message }}</div>
        </div>
        <div class="movies-grid">
          <div v-for="(movie, index) in movies" :key="index" class="flip-card">
            <div class="flip-card-inner">
              <div class="flip-card-front">
                <img class="image-custom" :src="`https://image.tmdb.org/t/p/w300${movie.poster_path}`" alt="movies">
              </div>
              <div class="flip-card-back">
                <h3>{{ movie.title }}</h3>
                <div>
                  <p>Date de parution:<br>{{ movie.release_date }}</p>
                  <hr>
                  <p>Note moyenne:<br>{{ movie.vote_average }}</p>
                  <hr>
                  <p>Genre:<br>{{ movie.genre_ids }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'IndexPage',
  data () {
    return {
      movies: [],
      searchedMovies: [],
      searchInput: '',
      loading: false
    }
  },
  async fetch () {
    this.loading = true
    if (this.searchInput === '') {
      await this.fetchMovies()
      return
    }
    if (this.searchInput !== '') {
      await this.searchMovies()
    }
    this.loading = false
  },
  fetchDelay: 5000,
  watch: {
    searchInput () {
      console.log(this.searchInput)
    }
  },
  methods: {
    // get movies from api
    async fetchMovies () {
      const data = await axios.get('https://api.themoviedb.org/3/movie/now_playing?api_key=030e4ae4fa04b8499f401b541536d268')
      const result = data
      result.data.results.forEach((movie) => {
        this.movies.push(movie)
      })
    },
    // async getGenres () {
    // const data = await axios.get('https://api.themoviedb.org/3/genre/movie/list?api_key=030e4ae4fa04b8499f401b541536d268')
    // const result = data
    // console.log(result)
    // result.data.results.forEach((movie) => {
    //   this.movies.push(movie)
    // })
    // }

    // get results research from api
    async searchMovies () {
      const data = axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=030e4ae4fa04b8499f401b541536d268&language=en-US&page=1&query=${this.searchInput}`
      )
      const result = await data
      result.data.results.forEach((movie) => {
        this.searchedMovies.push(movie)
      })
    },

    // initialize searchbar
    resetSearch () {
      this.searchInput = ''
      this.searchedMovies = []
    }
  }
}
</script>

<style lang="scss" scoped>
.home {
  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }
  .search {
    display: flex;
    padding: 32px 16px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 6px;
      font-size: 14px;
      border: none;
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
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
  width: 300px;
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
</style>
