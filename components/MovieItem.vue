<template>
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
        <p>Genre(s):<br>{{ getNames() }}</p>
        <button class="button-custom ma-2" @click="actionCreate">
          Voir plus
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MovieItem',
  props: {
    movie: {
      type: Object,
      default () {
        return {}
      }
    },
    genres: {
      type: Array,
      default () {
        return []
      }
    }
  },
  data () {
    return {
      movieGenreName: []
    }
  },
  methods: {
    actionCreate () {
      this.$emit('action-in-parent')
    },
    getNames () {
      let names = []
      let match = false
      names = this.movie.genre_ids.map((val) => {
        match = this.genres.find((genre) => {
          return genre.id === val
        })
        return match ? match.name : undefined
      })
      this.movieGenreName = new Array(names).toString().replace(/,/g, ' . ')
      return this.movieGenreName
    }
  }
}
</script>

<style lang="scss" scoped>

.image-custom {
  border-radius: 25px;
}
.flip-card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  transition: transform 0.8s;
  transform-style: preserve-3d;
}

/* Do an horizontal flip when move the mouse over the flip box container */
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
  width: 325px;
  height: 450px;
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

.results-custom {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin: 1%;
}

.button-custom {
  width: 80%;
  margin-top: 20px;
}

</style>
