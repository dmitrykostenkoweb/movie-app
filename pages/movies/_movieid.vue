<template>
  <!-- Loading -->
  <Loading v-if="$fetchState.pending" />

  <!-- Movie Info -->

  <div v-else class="single-movie container">
    <div class="background">
      <img
        class="background-img"
        :src="`https://image.tmdb.org/t/p/original/${movie.backdrop_path}`"
        alt=""
      />
    </div>

    <NuxtLink class="button" :to="{ name: 'index' }"> Back </NuxtLink>
    <div class="movie-info">
      <div class="movie-img">
        <img
          :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
          alt=""
        />
      </div>
      <div class="movie-content">
        <!-- Title -->
        <h1>
          <a target="_blank" :href="movie.homepage">{{ movie.title }} </a>
        </h1>

        <!-- Tagline -->
        <p v-if="movie.tagline" class="movie-fact tagline">
          <span>Tagline:</span> "{{ movie.tagline }}"
        </p>

        <!-- Genres -->
        <div class="movie-fact">
          <span>Genres:</span>
          <div v-for="(genr, id) in movie.genres" :key="id">
            {{ genr.name }},
          </div>
        </div>

        <!-- Released -->
        <p class="movie-fact">
          <span>Released:</span>
          {{
            new Date(movie.release_date).toLocaleString('en-us', {
              month: 'long',
              day: 'numeric',
              year: 'numeric',
            })
          }}
        </p>

        <!-- Duration -->
        <p class="movie-fact">
          <span>Duration:</span> {{ movie.runtime }} minutes
        </p>

        <!-- Budget -->
        <p class="movie-fact">
          <span>Budget:</span>
          {{
            movie.budget.toLocaleString('en-us', {
              style: 'currency',
              currency: 'USD',
            })
          }}
        </p>

        <!-- Revenue -->
        <p class="movie-fact">
          <span>Revenue:</span>
          {{
            movie.revenue.toLocaleString('en-us', {
              style: 'currency',
              currency: 'USD',
            })
          }}
        </p>

        <!-- Overview -->
        <p class="movie-fact">
          <span>Overview:</span>
          {{ movie.overview }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Loading from '../Loading.vue'
export default {
  components: { Loading },
  name: 'single-movie',
   head() {
    return {
      title: this.movie.title,
    }
  },
  data() {
    return {
      movie: '',
    }
  },

  async fetch() {
    await this.getSingleMovie()
  },
  fetchDelay: 300,

  methods: {
    async getSingleMovie() {
      console.log(`route: ${this.$route.params.movieid}`)

      try {
        const responseSingleMovie = await axios.get(
          `https://api.themoviedb.org/3/movie/${this.$route.params.movieid}?api_key=aafd5624e29d6f2b8ceb629bd5243be0&language=en-US`
        )
        this.movie = responseSingleMovie.data
      } catch (error) {
        console.log(`Error ${error}`)
      } finally {
        console.log(this.movie)
      }
    },
  },
}
</script>

<style lang="scss">
.background {
  position: relative;
  min-width: 100%;
  min-height: auto;
  z-index: -5;
  opacity: 0.2;
}

.background-img {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -10%);
  width: 120%;
  height: auto;
  object-fit: contain;
}

.single-movie {
  z-index: 20;
  color: #fff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 32px 16px;
  .button {
    align-self: flex-start;
    margin-bottom: 32px;
  }
  .movie-info {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 32px;
    color: #fff;
    @media (min-width: 800px) {
      flex-direction: row;
      align-items: flex-start;
    }
    .movie-img {
      img {
        max-height: 500px;
        width: 100%;
        @media (min-width: 800px) {
          max-height: 700px;
          width: initial;
        }
      }
    }
    .movie-content {
      h1 {
        font-size: 56px;
        font-weight: 400;
      }
      .movie-fact {
        display: flex;
        column-gap: 50px;
        flex-wrap: wrap;
        margin-top: 12px;
        font-size: 20px;
        line-height: 1.5;
        span {
          font-weight: 200;
          color: #c92502;
        }
      }
      .tagline {
        font-style: italic;
        span {
          font-style: normal;
        }
      }
    }
  }
}
</style>
