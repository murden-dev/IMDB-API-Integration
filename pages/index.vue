<template>
  <div class="container">
    <div class="header" :class="isDetail ? 'dark' : 'light'">
      <h3 @click="isDetail = false">TheMovie<span>Db</span></h3>
      <el-button
        v-if="!session_id"
        type="text"
        class="login-button"
        @click="showLoginModal"
      >
        Login
      </el-button>
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
    <div ref="carrousel" class="carrousel">
      <el-card
        v-for="movie in movieList"
        :key="movie.id"
        class="card"
        shadow="always"
        @click="showDetails(movie.id)"
      >
        <el-row>
          <el-col :span="10" class="card__height-modifier">
            <el-image
              class="card__thumbnail"
              :src="imageURL + movie.poster_path"
              fit="cover"
              lazy
            >
              <img slot="placeholder" src="@/assets/loading.gif" />
              <img slot="error" src="@/assets/error.png" />
            </el-image>
          </el-col>
          <el-col :span="14">
            <div class="movie__info">
              <h2 class="movie__title">{{ movie.title }}</h2>
              <time class="movie__release-date">
                {{ formatMovieReleaseDate(movie.release_date) }}
              </time>
              <p class="movie__excerpt">
                {{ formatMovieExcerpt(movie.overview) }}
              </p>
              <h3
                class="movie__rating"
                :class="
                  formatMovieRating(movie.vote_average) >= 70
                    ? 'good'
                    : formatMovieRating(movie.vote_average) >= 50
                    ? 'average'
                    : 'bad'
                "
              >
                {{ formatMovieRating(movie.vote_average) }}%
              </h3>
              <el-button class="movie__button" @click="showDetails(movie.id)">
                Leia mais...
              </el-button>
            </div>
          </el-col>
        </el-row>
      </el-card>
    </div>
    <Detail
      @close="closeDetail"
      :imageURL="imageURL"
      :isDetail="isDetail"
      :movie="movieDetails.data"
      :apiKey="apiKey"
      :session_id="session_id"
      :account_id="account_id"
    />
  </div>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'
import Detail from '@/components/Detail.vue'

export default {
  components: { Detail },
  data: () => ({
    apiKey: '?api_key=a323978454b63679a0646cf2fe7ca498',
    locale: '&language=pt-BR',
    detailURL: 'https://api.themoviedb.org/3/movie',
    imageURL: 'https://image.tmdb.org/t/p/original',
    newSessionURL: 'https://api.themoviedb.org/3/authentication/session/new',
    byPopularity: '&include_video=true&sort_by=popularity.desc',
    searchQuery: '',
    response: {},
    session_id: '',
    account_id: '',
    selectedMovie: Number,
    isDetail: false,
    movieDetails: {}
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
    if (
      this.$route.query.request_token &&
      this.$route.query.approved === 'true'
    ) {
      axios
        .post(
          `${this.newSessionURL}?api_key=a323978454b63679a0646cf2fe7ca498`,
          { request_token: this.$route.query.request_token }
        )
        .then((response) => {
          if (response.data.success === true) {
            this.session_id = response.data.session_id
          }
        })
        .then(() => {
          axios
            .get(
              `https://api.themoviedb.org/3/account${this.apiKey}&session_id=${this.session_id}`
            )
            .then((response) => {
              if (response.data.id) {
                this.account_id = response.data.id.toString()
              }
            })
        })
    }
  },
  methods: {
    closeDetail() {
      this.isDetail = false
    },
    showLoginModal() {
      this.$confirm(
        'Entre com sua conta TMDb ou, caso prefira, teste grátis. 😊 Fácil assim!',
        {
          distinguishCancelAndClose: true,
          confirmButtonText: 'Login com TMDb',
          cancelButtonText: 'Quero Testar',
          center: true
        }
      )
        .then(() => {
          axios
            .get(
              `https://api.themoviedb.org/3/authentication/token/new${this.apiKey}`
            )
            .then((response) => {
              const token = response.data.request_token
              location.replace(
                `https://www.themoviedb.org/authenticate/${token}?redirect_to=https://sharp-meitner-f61aea.netlify.com/`
              )
            })
        })
        .catch((action) => {
          this.$message({
            type: 'info',
            message:
              action === 'cancel'
                ? 'Changes discarded. Proceeding to a new route.'
                : 'Stay in the current route'
          })
        })
    },
    async showDetails(id) {
      this.selectedMovie = id
      this.movieDetails = await axios
        .get(
          `${this.detailURL}/${this.selectedMovie}${this.apiKey}${this.locale}&append_to_response=videos,images`
        )
        .finally(() => {
          this.isDetail = true
        })
    },
    async getPopularMovies() {
      this.$refs.carrousel.scrollTo(0, 0)
      const response = await axios.get(
        `${this.baseURL}${this.apiKey}${this.locale}${this.byPopularity}`
      )
      return response.data
    },
    debounceMovieSearch: _.debounce(
      async function searchMovieByTitle() {
        this.$refs.carrousel.scrollTo(0, 0)
        const response = await axios.get(
          `${this.baseURL}${this.apiKey}${this.locale}&query=${this.searchQuery}`
        )
        this.response = response.data
      },
      500,
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
          timestamp.getDate() < 9
            ? '0' + (timestamp.getDate() + 1)
            : timestamp.getDate() + 1
        }
          de
          ${months[timestamp.getMonth()]}
          de ${timestamp.getFullYear()}`
      return releaseDate
    },
    formatMovieExcerpt(unformatted) {
      const excerpt = unformatted.substring(0, 360) + '...'
      return excerpt
    },
    formatMovieRating(value) {
      return value * 10
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss">
:root {
  --primary: #01d276;
  --bg-dark: #081d25;
}
html,
body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Helvetica Neue', Arial, sans-serif, 'Apple Color Emoji',
    'Segoe UI Emoji', 'Segoe UI Symbol';
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
  position: relative;
  overflow: hidden;
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
  z-index: 999;
  width: 100%;
  height: 4rem;
  padding: 1rem 3rem;
  color: var(--primary);
  box-shadow: 0px 4px 32px -12px #00000020;
  transition: all 240ms linear;
  transition-delay: 320ms;
  &.light {
    background-color: white;
    border-bottom: 1px solid #d7d7d7;
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
  &.dark {
    background-color: var(--bg-dark);
    border-bottom: none;
    h3 {
      cursor: pointer;
      &:hover {
        color: #d7d7d7;
        span {
          color: var(--primary);
        }
      }
    }
  }
  .login-button {
    font-size: 1rem;
    /*SAFARI FIX*/
    margin-left: 100%;
    margin-left: auto;
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

.carrousel {
  width: calc(100vw + 2rem);
  // FIX FOR CHILD BOX-SHADOW
  padding: 2rem 2rem;
  margin-left: -3rem;
  height: calc(60vh + 4rem);
  display: flex;
  -webkit-overflow-scrolling: touch;
  overflow-x: scroll;
  scroll-padding: 3rem;
  scroll-snap-type: x mandatory;
  scroll-snap-stop: always;
  cursor: scroll;
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
  will-change: box-shadow;
  &:first-child {
    margin-left: 1rem;
  }
  &:hover {
    transform: translateY(-0.4rem);
    box-shadow: 0 4px 16px 0 rgba(0, 0, 0, 0.25);
  }
  .card__height-modifier {
    height: 60vh;
  }
  .el-card__body {
    padding: 0;
    overflow: hidden;
  }
  .card__thumbnail {
    height: auto;
    height: -webkit-fill-available;
    width: 105%;
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
      opacity: 0.95;
      margin-bottom: 0.4rem;
    }
    .movie__release-date {
      opacity: 0.45;
      margin-bottom: 1.2rem;
      display: block;
    }
    .movie__excerpt {
      opacity: 0.85;
      margin-bottom: 1rem;
      text-overflow: ellipsis;
    }
    .movie__rating {
      font-size: 2.4rem;
      position: absolute;
      bottom: 2rem;
      left: calc(42% + 2rem);
      &.good {
        color: var(--primary);
      }
      &.average {
        color: orange;
      }
      &.bad {
        color: red;
      }
    }
    .movie__button {
      position: absolute;
      bottom: 2rem;
      right: 2rem;
    }
  }
}
</style>
