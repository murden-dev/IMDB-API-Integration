<template>
  <DetailContainer
    class="detail-container"
    :class="isDetail ? 'visible' : ''"
    :pose="isDetail ? 'visible' : 'hidden'"
  >
    <div class="backdrop"></div>
  </DetailContainer>
</template>

<script>
import posed from 'vue-pose'

export default {
  name: 'Details',
  components: {
    DetailContainer: posed.div({
      visible: {
        opacity: 1,
        translateY: 0,
        staggerChildren: 100,
        transition: {
          opacity: { ease: 'linear', duration: 200 },
          default: { ease: 'spring', stiffness: 100, duration: 500 }
        }
      },
      hidden: {
        opacity: 0,
        translateY: 100,
        transition: {
          opacity: { ease: 'linear', duration: 200 },
          default: { ease: 'spring', stiffness: 100, duration: 500 }
        }
      },
      initialPose: 'hidden'
    })
  },
  props: {
    isDetail: Boolean,
    movie: {}
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.detail-container {
  position: absolute;
  top: 0;
  left: 0;
  background-color: black;
  z-index: -10;
  height: 100vh;
  width: 100vw;
  overflow-y: scroll;
  transition: transform 450ms ease-out, opacity 200ms linear;
  &.visible {
    z-index: 998;
  }
}
.backdrop {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 35vh;
}
</style>
