<template>
  <div class="movies-grid" id="movie-grid">
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
</template>

<script>
export default {
  name: 'movieListView',
  props: {
    movies: {
      type: Array,
      required: true,
    },
  },
}
</script>

<style lang="scss" scoped>
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
</style>
