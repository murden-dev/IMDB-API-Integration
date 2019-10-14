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
        <time class="movie__runtime"> {{ movieRuntime }}min </time>
        <time class="movie__release-date">
          {{ formatMovieReleaseDate(movieReleaseDate) }}
        </time>
        <h3>Sinopse</h3>
        <p class="movie__overview">{{ movieOverview }}</p>
        <el-divider />
        <quote>{{ movieTagline }}</quote>
        <div>
          <h5></h5>
          <p></p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Detail',
  props: {
    isDetail: Boolean,
    imageURL: String,
    movie: Object
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
  transition: transform 700ms ease-out, opacity 200ms linear;
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
    transition-delay: 900ms;
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
      line-height: 1.4;
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
    quote {
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
        transition-delay: 1600ms;
        opacity: 1;
        transform: translateY(0);
      }
      h3 {
        transition-delay: 1800ms;
        opacity: 1;
        transform: translateY(0);
      }
      p {
        transition-delay: 1800ms;
        opacity: 1;
        transform: translateY(0);
      }
      time {
        transition-delay: 1700ms;
        opacity: 1;
        transform: translateY(0);
      }
      quote {
        transition-delay: 2000ms;
        opacity: 1;
        transform: translateY(0);
      }
    }
  }
}
</style>
