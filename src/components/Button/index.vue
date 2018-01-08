<template>
  <div class="yo-btn" 
    :class="{
      'btn-lg': size == 'lg',
      'btn-md': size == 'md',
      'btn-sm': size == 'sm'
      }" 
    :style="{
      backgroundColor: backgroundColor
    }">
    <slot></slot>
  </div>
</template>

<script>
export default {

  name: 'YoButton',

  props: {
    size: {
      type: String,
      default: 'md'
    },

    backgroundColor: {
      type: String
    }
  },

  data () {
    return {

    }
  }
}
</script>

<style lang="stylus" scoped>
@import '../theme.styl';
.yo-btn
  background-color $themeColor
  padding 6px 10px
  text-align center
  color #fff
  border-radius 4px
  line-height 1em
  &.disabled
    background-color #d1d1d1!important
    color #f5f5f5
.btn-lg
  font-size $fontSizeLg
  padding 14px 18px
.btn-md
  font-size $fontSizeMd
  padding 12px 18px
.btn-sm
  font-size $fontSizeSm
  padding 8px 11px
</style>