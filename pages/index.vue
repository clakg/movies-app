<template>
  <div>
    <!-- Movies -->
    <div class="container">
      <div>
        <div v-for="(movie, index) in movies" :key="index">
          <div>
            <img :src="`https://image.tmdb.org/t/p/w300${movie.poster_path}`" alt="">
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
          <!-- {{ movie }} -->
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
