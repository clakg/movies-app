<template>
  <div>
    <!-- Movies -->
    <div class="container">
      <h1 class="title-custom">
        STEEPLE MOVIES APP
      </h1>
      <div class="movies-grid">
        <div v-for="(movie, index) in movies" :key="index" class="movie">
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w300${movie.poster_path}`" alt="movies">
          </div>
          <div>
            {{ movie.title }}
          </div>
          <div>
            {{ movie.release_date }}
          </div>
          <div>
            {{ movie.vote_average }}
          </div>
          <div>
            {{ movie.genre_ids }}
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
      movies: []
    }
  },
  async fetch () {
    await this.fetchMovies()
  },
  methods: {
    async fetchMovies () {
      const data = await axios.get('https://api.themoviedb.org/3/movie/now_playing?api_key=030e4ae4fa04b8499f401b541536d268')
      const result = data
      // console.log(result.data.results)
      result.data.results.forEach((movie) => {
        this.movies.push(movie)
      })
    }
    // async getGenres () {
    // const data = await axios.get('https://api.themoviedb.org/3/genre/movie/list?api_key=030e4ae4fa04b8499f401b541536d268')
    // const result = data
    // console.log(result)
    // result.data.results.forEach((movie) => {
    //   this.movies.push(movie)
    // })
    // }
  }
}
</script>

<style lang="scss" scoped>
.title-custom {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 2%;
}
.movies-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(3, 1fr);
  grid-column-gap: 10px;
  grid-row-gap: 10px;
  @media (min-width: 500px) {
    grid-template-columns: repeat(1, 1fr);
  }
  @media (min-width: 750px) {
    grid-template-columns: repeat(3, 1fr);
  }
  @media (min-width: 1100px) {
    grid-template-columns: repeat(4, 1fr);
  }
  .movie {
    position: relative;
    display: flex;
    flex-direction: column;
    .movie-img {
      position: relative;
      overflow: hidden;
      &:hover {
        .overview {
          transform: translateY(200);
        }
      }
      img {
        display: block;
        width: 100%;
        height: 100%;
        border-radius: 25px;
      }
    }
  }
}
</style>
