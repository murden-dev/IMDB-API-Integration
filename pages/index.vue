<template>
  <div class="container">
    <el-page-header content="TheMovieDB" class="header" @back="goBack" />
    <el-input
      v-model="searchQuery"
      class="search-bar"
      name="SearchBar"
      placeholder="Buscar por título"
    >
      <el-button slot="append" type="primary" icon="el-icon-search" />
    </el-input>
    <SearchResults />
    <h1 class="total">128 resultados para "{{ searchQuery }}"</h1>
    <el-row :gutter="32">
      <el-col :span="12">
        <el-card class="card" shadow="hover">
          <el-row>
            <el-col :span="12">
              <el-image
                class="thumbnail"
                :src="require('../assets/joker.jpg')"
                fit="cover"
              />
            </el-col>
            <el-col :span="12">
              <div class="card__inner">
                <h2 class="title">The Joker</h2>
                <time class="timestamp">09 de Outubro de 2019</time>
                <p class="excerpt">
                  During the 1980s, a failed stand-up comedian is driven insane
                  and turns to a life of crime and chaos in Gotham City while
                  becoming an infamous psychopathic crime figure.
                </p>
                <el-button plain>Leia Mais...</el-button>
              </div>
            </el-col>
          </el-row>
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card class="card" shadow="hover">
          <el-row>
            <el-col :span="12">
              <el-image
                class="thumbnail"
                :src="require('../assets/joker.jpg')"
                fit="cover"
              />
            </el-col>
            <el-col :span="12">
              <div class="card__inner">
                <h2 class="title">The Joker</h2>
                <time class="timestamp">09 de Outubro de 2019</time>
                <p class="excerpt">
                  During the 1980s, a failed stand-up comedian is driven insane
                  and turns to a life of crime and chaos in Gotham City while
                  becoming an infamous psychopathic crime figure.
                </p>
                <el-button plain>Leia Mais...</el-button>
              </div>
            </el-col>
          </el-row>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  data: () => ({
    searchQuery: ''
  }),
  methods: {
    goBack() {
      this.$router.push(-1)
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss">
html,
body {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
}

.total {
  margin-bottom: 1.2rem;
}

.title {
  margin-bottom: 0.4rem;
}

.timestamp {
  margin-bottom: 2rem;
  display: block;
}

.excerpt {
  margin-bottom: 1rem;
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
  background-color: white;
  box-shadow: 0px 4px 32px -12px #00000020;
  border-bottom: 1px solid #e4e4e4;
}

.search-bar {
  margin-bottom: 2rem;
}

.card {
  width: auto;
  border-radius: 4px;
  .el-card__body {
    padding: 0;
    overflow: hidden;
  }
  .card__inner {
    padding: 2rem 1rem 1rem 1.2rem;
  }
}

.thumbnail {
  height: 102%;
  width: 100%;
  overflow: visible;
}
</style>
