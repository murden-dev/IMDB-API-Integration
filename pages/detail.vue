<template>
  <div class="container">
    <div class="header">
      <h3>TheMovie<span>Db</span></h3>
    </div>
    <el-input
      v-model="searchQuery"
      class="search-bar"
      name="SearchBar"
      placeholder="Buscar por título"
      @input="debounceMovieSearch()"
    >
      <el-button slot="append" type="primary" icon="el-icon-search" />
    </el-input>
    <h2 v-if="searchQuery.length" class="headline">
      <span>{{ totalResults }}</span>
      resultados para
      <span>"{{ searchQuery }}"</span>
    </h2>
    <h2 v-else class="headline">
      <span>Mais Populares</span>
    </h2>
    <div class="carrousel">
      <el-card
        v-for="movie in movieList"
        :key="movie.id"
        class="card"
        shadow="always"
      >
        <el-row>
          <el-col :span="12" class="card__height-modifier">
            <el-image
              class="card__thumbnail"
              :src="imageURL + movie.poster_path"
              fit="cover"
              lazy
            >
              <img
                slot="placeholder"
                class="loading-image"
                src="@/assets/loading.gif"
              />
              <img slot="error" class="error-image" src="@/assets/error.png" />
            </el-image>
          </el-col>
          <el-col :span="12">
            <div class="movie__info">
              <h2 class="movie__title">{{ movie.title }}</h2>
              <time class="movie__release-date">
                {{ formatMovieReleaseDate(movie.release_date) }}
              </time>
              <p class="movie__excerpt">
                {{ formatMovieExcerpt(movie.overview) }}
              </p>
              <el-button class="movie__button">Leia mais...</el-button>
            </div>
          </el-col>
        </el-row>
      </el-card>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'

export default {
  data: () => ({
    apiKey: '?api_key=a323978454b63679a0646cf2fe7ca498&language=pt-BR&page=1',
    imageURL: 'https://image.tmdb.org/t/p/original',
    byPopularity: '&include_video=true&sort_by=popularity.desc',
    searchQuery: '',
    response: {}
  }),
  computed: {
    baseURL() {
      return this.searchQuery.length > 0
        ? 'https://api.themoviedb.org/3/search/movie'
        : 'https://api.themoviedb.org/3/discover/movie'
    },
    totalResults() {
      return this.response.total_results
    },
    page() {
      return this.response.page
    },
    movieList() {
      return this.response.results
    }
  },
  mounted() {
    this.getPopularMovies().then((response) => (this.response = response))
  },
  methods: {
    goBack() {
      this.$router.push(-1)
    },
    async getPopularMovies() {
      const response = await axios.get(
        `${this.baseURL}${this.apiKey}${this.byPopularity}`
      )
      return response.data
    },
    debounceMovieSearch: _.debounce(
      async function searchMovieByTitle() {
        const response = await axios.get(
          `${this.baseURL}${this.apiKey}&query=${this.searchQuery}`
        )
        this.response = response.data
      },
      1000,
      {
        trailing: true,
        leading: false
      }
    ),
    formatMovieReleaseDate(unformatted) {
      const months = [
        'Janeiro',
        'Fevereiro',
        'Março',
        'Abril',
        'Maio',
        'Junho',
        'Julho',
        'Agosto',
        'Setembro',
        'Outubro',
        'Novembro',
        'Dezembro'
      ]
      const timestamp = new Date(unformatted)
      const releaseDate = `
        ${
          timestamp.getDate() < 10
            ? '0' + (timestamp.getDate() + 1)
            : timestamp.getDate() + 1
        }
          de
          ${months[timestamp.getMonth()]}
          de ${timestamp.getFullYear()}`
      return releaseDate
    },
    formatMovieExcerpt(unformatted) {
      const excerpt = unformatted.substring(0, 240) + '...'
      return excerpt
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss">
:root {
  --primary: #01d276;
}
html,
body {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
}

.results-enter,
.results-leave-to {
  height: 0rem;
  opacity: 0;
  will-change: height;
}

.results-enter-to,
.results-leave {
  height: 2rem;
  opacity: 1;
  will-change: height;
}

.results-leave-active {
  transition: height 300ms ease-in, opacity 100ms linear;
}

.results-enter-active {
  transition: height 300ms ease-out, opacity 100ms linear;
}

.accent {
  color: var(--primary);
}

.headline {
  font-size: 2rem;
  span {
    color: var(--primary);
  }
}

.container {
  padding: 6rem 3rem;
  margin: 0;
  height: 100vh;
}

.header {
  position: fixed;
  display: flex;
  align-items: center;
  top: 0;
  left: 0;
  width: 100%;
  height: 4rem;
  padding: 1rem 3rem;
  color: var(--primary);
  background-color: white;
  box-shadow: 0px 4px 32px -12px #00000020;
  border-bottom: 1px solid #e4e4e4;
  h3 {
    cursor: pointer;
    &:hover {
      color: initial;
      span {
        color: var(--primary);
      }
    }
  }
}

.search-bar {
  margin-bottom: 2rem;
  .el-input__inner:focus,
  .el-input__inner:hover {
    border-color: var(--primary);
  }
  .el-input-group__append {
    border-color: var(--primary);
    background-color: var(--primary);
    color: #ffffff;
  }
}

.card {
  max-width: 60%;
  height: 60vh;
  margin-right: 3rem;
  border: 0.5px solid #01d276;
  border-radius: 6px;
  display: flex;
  flex: 1 0 100%;
  scroll-snap-align: start;
  overflow: none;
  .card__height-modifier {
    height: 60vh;
  }
  .el-card__body {
    padding: 0;
    overflow: hidden;
  }
  .card__thumbnail {
    height: 100%;
    height: -webkit-fill-available;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }
  .movie__info {
    height: 100%;
    padding: 2rem 1rem 1rem 2rem;
    display: flex;
    flex-direction: column;
    .movie__title {
      margin-bottom: 0.4rem;
    }

    .movie__release-date {
      margin-bottom: 1.2rem;
      display: block;
    }

    .movie__excerpt {
      margin-bottom: 1rem;
      text-overflow: ellipsis;
    }
    .movie__button {
      position: absolute;
      bottom: 2rem;
      right: 2rem;
    }
  }
}

.carrousel {
  width: 100%;
  height: calc(60vh + 4rem);
  padding: 2rem 2rem 2rem 0;
  display: flex;
  -webkit-overflow-scrolling: touch;
  overflow-x: scroll;
  scroll-snap-type: x mandatory;
  scroll-snap-stop: always;
}
</style>
