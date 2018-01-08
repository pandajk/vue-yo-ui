<template>
  <div class="Pupop">
    <div class="Pupop-mask" v-if="visible" @click="hide"></div>  
    <transition name="slide">
      <div class="Popup-wrapper" v-if="visible">
        <slot></slot>
      </div>
    </transition>
  </div>
</template>

<script>
export default {

  name: 'Pupop',

  props: ['show'],

  data () {
    return {
      visible: false
    }
  },
  watch: {
    show(n, o) {
      this.visible = n;
    }
  },
  methods: {
    hide() {
      this.visible = false
    }
  },

}
</script>

<style lang="stylus" scoped>
.slide-enter-active
  transform none
.slide-leave-active
  transform translate3d(0, 100%, 0)
.slide-enter
  transform translate3d(0, 100%, 0)
.slide-leave
  transform none

.Pupop-mask
  width 100%
  height 100%
  position fixed
  top 0
  left 0
  z-index 400
  background-color rgba(0,0,0,.5)

.Popup-wrapper
  position fixed
  width 100%
  bottom 0
  transition all .3s ease-in
  z-index 500
</style>