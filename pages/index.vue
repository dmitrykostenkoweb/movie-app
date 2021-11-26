<template>
  <div class="home">
    <!-- Hero -->
    <Hero />
    <!-- Search -->
    <div class="container search">
      <input
        @keyup.enter="$fetch"
        type="text"
        v-model.lazy="searchInput"
        placeholder="Search"
      />
      <button @click="clearSearch" v-show="searchInput !== ''" class="button">
        Clear Search
      </button>
    </div>
    <!-- loading -->
    <Loading v-if="$fetchState.pending" />
    <!-- Movies -->
    <div class="container movies">
      <!--Searched Movies -->

      <div v-if="searchInput !== ''" class="movies-grid" id="movie-grid">
        <div class="movie" v-for="(movie, idx) in searchedMovies" :key="idx">
          <div class="movie-img">
            <img
              :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
              :alt="`${movie.title} poster`"
            />
            <p class="review">{{ movie.vote_average }}</p>
            <p class="overview">{{ movie.overview }}</p>
          </div>
          <div class="info">
            <p class="title">
              {{ movie.title.slice(0, 25)
              }}<span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{ movie.release_date }}
            </p>
            <NuxtLink
              class="button button-light"
              :to="{
                name: 'movies-movieid',
                params: {
                  movieid: movie.id,
                },
              }"
            >
              Get more info</NuxtLink
            >
          </div>
        </div>
      </div>
      <!-- Now Streaming -->
      <div v-else class="movies-grid" id="movie-grid">
        <div class="movie" v-for="(movie, idx) in movies" :key="idx">
          <div class="movie-img">
            <img
              :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
              :alt="`${movie.title} poster`"
            />
            <p class="review">{{ movie.vote_average }}</p>
            <p class="overview">{{ movie.overview }}</p>
          </div>
          <div class="info">
            <p class="title">
              {{ movie.title.slice(0, 25)
              }}<span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{ movie.release_date }}
            </p>
            <NuxtLink
              class="button button-light"
              :to="{
                name: 'movies-movieid',
                params: {
                  movieid: movie.id,
                },
              }"
            >
              Get more info</NuxtLink
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Loading from './Loading.vue'
export default {
  components: { Loading },
  data() {
    return {
      movies: [],
      searchedMovies: [],
      searchInput: '',
    }
  },
  async fetch() {
    if (this.searchInput === '') {
      await this.getMovies()
      return
    }

    await this.searchMovies()
  },
  fetchDelay: 300,
  methods: {
    async getMovies() {
      try {
        const responseMovies = await axios.get(
          `https://api.themoviedb.org/3/movie/now_playing?api_key=aafd5624e29d6f2b8ceb629bd5243be0&language=en-US&page=1`
        )
        responseMovies.data.results.forEach((movie) => {
          this.movies.push(movie)
        })
      } catch (error) {
        alert(`Error ${error}`)
      } finally {
      }
    },
    async searchMovies() {
      const responseSearchedMovies = await axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=aafd5624e29d6f2b8ceb629bd5243be0&language=en-US&query=${this.searchInput}&page=1&include_adult=false`
      )
      responseSearchedMovies.data.results.forEach((movie) => {
        this.searchedMovies.push(movie)
      })
      console.log(this.searchedMovies)
    },
    clearSearch() {
      this.searchedMovies = []
      this.searchInput = ''
    },
  },
}
</script>
<style lang="scss" scoped>
.loading {
  padding-top: 120px;
  align-items: flex-starts;
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
}
// .....
.movies {
  padding: 32px 16px;
  .movies-grid {
    display: grid;
    column-gap: 32px;
    row-gap: 64px;
    grid-template-columns: 1fr;
    @media (min-width: 500px) {
      grid-template-columns: repeat(2, 1fr);
    }
    @media (min-width: 750px) {
      grid-template-columns: repeat(2, 1fr);
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
            transform: translateY(0);
          }
        }
        img {
          display: block;
          width: 100%;
          height: 100%;
        }
        .review {
          position: absolute;
          top: 0;
          left: 0;
          display: flex;
          justify-content: center;
          align-items: center;
          width: 40px;
          height: 40px;
          background-color: #c92502;
          color: #fff;
          border-radius: 0 0 16px 0;
          box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
            0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
        .overview {
          line-height: 1.5;
          position: absolute;
          bottom: 0;
          background-color: rgba(201, 38, 2, 0.5);
          padding: 12px;
          color: #fff;
          transform: translateY(100%);
          transition: 0.3s ease-in-out all;
        }
      }
      .info {
        margin-top: auto;
        .title {
          margin-top: 8px;
          color: #fff;
          font-size: 20px;
        }
        .release {
          margin-top: 8px;
          color: #c9c9c9;
        }
        .button {
          margin-top: 8px;
        }
      }
    }
  }
}
</style>
