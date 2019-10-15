<template>
  <div class="detail-container" :class="isDetail ? 'visible' : 'hidden'">
    <div class="detail-header">
      <el-image
        class="backdrop"
        :src="backdropURL"
        fit="cover"
        :class="isDetail ? 'visible' : 'hidden'"
      />
      <el-image
        class="poster"
        :src="posterURL"
        fit="cover"
        :class="isDetail ? 'visible' : 'hidden'"
      />
      <div
        class="detail-header__content"
        :class="isDetail ? 'visible' : 'hidden'"
      >
        <h1
          class="movie__rating"
          :class="
            movieRating >= 70 ? 'good' : movieRating >= 50 ? 'average' : 'bad'
          "
        >
          {{ movieRating }}%
        </h1>
        <h1>{{ movieTitle }}</h1>
        <div class="movie__release-info">
          <div class="movie__release-info__inner">
            <time class="movie__runtime"> {{ movieRuntime }}min </time>
            <time class="movie__release-date">
              {{ formatMovieReleaseDate(movieReleaseDate) }}
            </time>
          </div>
          <div class="actionButtons" :class="isDetail ? 'visible' : 'hidden'">
            <el-button
              icon="el-icon-star-off"
              v-if="movie && account_id"
              circle
              @click="markAsFavorite(movie.id)"
            />
            <el-button
              icon="el-icon-plus"
              v-if="movie && account_id"
              round
              @click="addToWatchlist(movie.id)"
            >
              Minha Lista
            </el-button>
            <el-button
              icon="el-icon-video-play"
              v-if="movie && account_id"
              type="text"
              @click="watchTrailer()"
            >
              Assistir Trailer
            </el-button>
          </div>
        </div>
        <h3>Sinopse</h3>
        <p class="movie__overview">{{ movieOverview }}</p>
        <el-divider />
        <q>{{ movieTagline }}</q>
      </div>
    </div>
    <el-button
      type="text"
      class="closeButton"
      icon="el-icon-close"
      @click="closeDetail"
    />
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Detail',
  props: {
    isDetail: Boolean,
    imageURL: String,
    movie: Object,
    apiKey: String,
    session_id: String,
    account_id: String
  },
  computed: {
    backdropURL() {
      return this.isDetail && this.movie
        ? this.imageURL + this.movie.backdrop_path
        : ''
    },
    movieRating() {
      return this.isDetail && this.movie ? this.movie.vote_average * 10 : ''
    },
    movieTitle() {
      return this.isDetail && this.movie ? this.movie.title : ''
    },
    movieReleaseDate() {
      return this.isDetail && this.movie ? this.movie.release_date : ''
    },
    movieOverview() {
      return this.isDetail && this.movie ? this.movie.overview : ''
    },
    movieTagline() {
      return this.isDetail && this.movie ? this.movie.tagline : ''
    },
    movieRuntime() {
      return this.isDetail && this.movie ? this.movie.runtime : ''
    },
    posterURL() {
      return this.isDetail && this.movie
        ? this.imageURL + this.movie.poster_path
        : ''
    }
  },
  methods: {
    formatMovieReleaseDate(unformatted) {
      if (this.isDetail && this.movie) {
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
      }
    },
    markAsFavorite(id) {
      axios
        .post(
          `https://api.themoviedb.org/3/account/${this.account_id}/favorite${this.apiKey}&session_id=${this.session_id}`,
          {
            media_type: 'movie',
            media_id: id,
            favorite: true
          }
        )
        .then((response) => {
          this.$message({
            type: 'success',
            message: 'Adicionado aos favoritos!'
          })
        })
    },
    addToWatchlist(id) {
      axios
        .post(
          `https://api.themoviedb.org/3/account/${this.account_id}/watchlist${this.apiKey}&session_id=${this.session_id}`,
          {
            media_type: 'movie',
            media_id: id,
            watchlist: true
          }
        )
        .then((response) => {
          this.$message({
            type: 'success',
            message: 'Adicionado à sua lista!'
          })
        })
    },
    watchTrailer() {
      const video = this.movie ? this.movie.videos.results[0].key : ''
      window.open(`https://www.youtube.com/watch?v=${video}`)
    },
    closeDetail() {
      this.$emit('close')
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.detail-container {
  position: absolute;
  top: 0;
  left: 0;
  max-height: 100vh;
  max-width: 100vw;
  height: 100vh;
  width: 100vw;
  padding-top: 8rem;
  overflow-y: hidden;
  overflow-x: hidden;
  transform: translateY(100%);
  transition: transform 500ms ease-out, opacity 200ms linear;
  &.visible {
    transform: translateY(0);
    z-index: 998;
    background-color: #010101;
  }
}
.backdrop {
  width: 100vw;
  height: 100vh;
  opacity: 0;
  filter: brightness(1);
  transform: translateY(0);
  transition: transform 600ms ease-out, opacity 800ms linear;
  will-change: filter;
  &.visible {
    filter: brightness(0.2);
    opacity: 1;
    transform: translateY(-4rem);
  }
}

.poster {
  width: 24vw;
  height: auto;
  position: absolute;
  left: 5.6rem;
  top: 10rem;
  overflow: hidden;
  border-radius: 6px;
  opacity: 0;
  transform: translateY(3.2rem);
  transition: transform 600ms ease-out, opacity 800ms linear;
  box-shadow: 0 4px 16px 0 rgba(0, 0, 0, 0);
  &.visible {
    transition-delay: 700ms;
    opacity: 1;
    transform: translateY(2rem);
    box-shadow: 0 4px 16px 0 rgba(0, 0, 0, 0.8);
  }
}

.detail-header {
  z-index: 999;
  display: flex;
  margin: 0 auto;
  .detail-header__content {
    position: absolute;
    top: 0;
    left: 40%;
    width: 60vw;
    padding: 5.6rem 5.6rem 0 0;
    height: 100%;
    display: flex;
    align-items: flex-start;
    justify-content: center;
    flex-direction: column;
    color: #e9e9e9;
    h1 {
      transition: all 200ms ease;
      font-size: 3.2rem;
      font-weight: bolder;
      margin-bottom: 0.6rem;
      transform: translateY(0.4rem);
      opacity: 0;
      &.movie__rating {
        font-size: 2.4rem;
        position: relative;
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
    }
    h3 {
      transition: all 600ms ease;
      font-size: 1.2rem;
      font-weight: bolder;
      margin-bottom: 0.6rem;
      transform: translateY(0.4rem);
      opacity: 0;
    }
    p {
      transition: all 600ms ease;
      font-size: 1rem;
      line-height: 1.5;
      transform: translateY(0.4rem);
      opacity: 0;
    }
    time {
      transition: all 600ms ease;
      font-size: 1rem;
      opacity: 0.8;
      margin-bottom: 2rem;
      transform: translateY(0.4rem);
      opacity: 0;
      &.movie__runtime {
        margin-bottom: 0.4rem;
        font-weight: lighter;
        font-style: italic;
      }
    }
    q {
      transition: all 600ms ease;
      font-size: 2rem;
      font-weight: bolder;
      margin-bottom: 0.6rem;
      font-weight: lighter;
      transform: translateY(0.4rem);
      opacity: 0;
    }
    &.visible {
      h1 {
        transition-delay: 1400ms;
        opacity: 1;
        transform: translateY(0);
      }
      h3 {
        transition-delay: 1600ms;
        opacity: 1;
        transform: translateY(0);
      }
      p {
        transition-delay: 1600ms;
        opacity: 1;
        transform: translateY(0);
      }
      time {
        transition-delay: 1400ms;
        opacity: 1;
        transform: translateY(0);
      }
      q {
        transition-delay: 2000ms;
        opacity: 1;
        transform: translateY(0);
      }
    }
    .movie__release-info {
      width: 100%;
      display: flex;
      flex-direction: row;
      align-items: flex-start;
      justify-content: space-between;
      .movie__release-info__inner {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
      }
      .actionButtons {
        display: flex;
        flex-direction: row;
        align-items: flex-end;
        justify-content: space-around;
        justify-content: space-evenly;
        opacity: 0;
        transform: translateY(0.8rem);
        transition: all 600ms ease-out;
        &.visible {
          transition-delay: 1450ms;
          opacity: 1;
          transform: translateY(0);
        }
      }
    }
  }
}
.closeButton {
  position: absolute;
  right: 2rem;
  top: 4.8rem;
  font-size: 2rem;
}
</style>
