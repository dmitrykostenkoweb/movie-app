<template>
  <div class="home">
    <app-bar v-on:navClick="selectNav" />
    <!-- Hero -->
    <Hero :title="selectedNav" />
    <!-- Search -->
    <div class="container search">
      <input
        @keyup.enter="$fetch"
        type="text"
        v-model.lazy="searchInput"
        placeholder="Search"
        @focus="clearSearch"
      />
      <button @click="clearSearch" v-show="searchInput !== ''" class="button">
        Clear Search
      </button>
    </div>
    <!-- loading -->
    <loading v-if="$fetchState.pending" />
    <!-- Error Message -->
    <error-message :message="errorMessage" v-if="errorMessage !== ''" />
    <!-- Movies -->
    <div v-else class="container movies">
      <!--Searched Movies -->
      <movie-list-view v-if="searchInput !== ''" :movies="searchedMovies" />

      <!-- Now Streaming -->
      <movie-list-view v-else :movies="movies" />

      <div ref="observer" class="observer"></div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Loading from './Loading.vue'
import MovieListView from '../components/movieListView.vue'
import AppBar from '../components/AppBar.vue'
import ErrorMessage from '../components/ErrorMessage.vue'
export default {
  head() {
    return {
      title: 'Movie App - Latest Streaming Movie Info ',
      meta: [{ hid: 'description', name: 'description', content: 'movies' }],
    }
  },
  components: { Loading, MovieListView, AppBar, ErrorMessage },
  data() {
    return {
      movies: [],
      searchedMovies: [],
      searchInput: '',
      page: 1,
      selectedNav: 'Now playing',
      apiKey: 'aafd5624e29d6f2b8ceb629bd5243be0',
      apiPart: 'now_playing',
      errorMessage: '',
    }
  },
  async fetch() {
    if (this.searchInput === '') {
      await this.getMovies()
      return
    }

    await this.searchMovies()
  },
  fetchDelay: 500,
  mounted() {
    this.getMoreMovies()
  },
  methods: {
    async getMovies() {
      this.errorMessage = ''

      try {
        const responseMovies = await axios.get(
          `https://api.themoviedb.org/3/movie/${this.apiPart}?api_key=${this.apiKey}&language=en-US&page=${this.page}`
        )
        responseMovies.data.results.forEach((movie) => {
          this.movies.push(movie)
        })
      } catch (error) {
        console.log(`Error ${error}`)
        this.errorMessage = 'Something wrong 😕'
      } finally {
        if (this.movies.length === 0) {
          this.errorMessage = 'Something wrong 😕'
        }
      }
    },
    async searchMovies() {
      this.page = 1
      this.errorMessage = ''

      try {
        const responseSearchedMovies = await axios.get(
          `https://api.themoviedb.org/3/search/movie?api_key=${this.apiKey}&language=en-US&query=${this.searchInput}&page=${this.page}&include_adult=false`
        )
        responseSearchedMovies.data.results.forEach((movie) => {
          this.searchedMovies.push(movie)
        })
      } catch (error) {
        console.log(`Error ${error}`)
        this.errorMessage = 'Something wrong 😕'
      } finally {
        if (this.searchedMovies.length === 0) {
          this.errorMessage = 'No results 😕'
        }
      }
    },
    clearSearch() {
      this.searchedMovies = []
      this.searchInput = ''
      this.errorMessage = ''
    },
    getMoreMovies() {
      // observer
      const options = {
        rootMargin: '0px',
        threshold: 1.0,
      }
      const callback = (entries, observer) => {
        if (entries[0].isIntersecting) {
          this.page += 1
          this.$fetch()
        }
      }
      const observer = new IntersectionObserver(callback, options)
      observer.observe(this.$refs.observer)
    },
    selectNav(nav) {
      this.selectedNav = nav
      this.movies = []
      this.page = 1
      this.changeApi()
    },
    changeApi() {
      this.apiPart = this.selectedNav.toLowerCase().replace(/ /g, '_')
      console.log(this.apiPart)
      this.$fetch()
    },
  },
}
</script>
<style lang="scss">
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
}
</style>
