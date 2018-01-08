<template>
  <div class="yo-scroller" @touchmove="preventMove">
    <div class="yo-scroller-wrapper" @touchmove="touchmove" @touchstart="touchstart" :ref="`scroller${id}`">
      <div>
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {

  name: 'Scroller',

  data () {
    return {
      id: +new Date()
    }
  },

  methods: {
    preventMove(e){
      e.preventDefault();
    },

    touchstart(e) {
      this.start = e.touches[0];
    },
    
    touchmove(e) {
      let touch = e.touches[0];

      let target = this.$refs[`scroller${this.id}`]

      let isAtTop = this.start.screenY<=touch.screenY && target.scrollTop == 0;
      let isAtBottom = this.start.screenY>=touch.screenY && target.scrollTop+ target.offsetHeight>=target.childNodes[0].offsetHeight ;

      if(isAtTop || isAtBottom){
          e.preventDefault();
          return
      }

        e.stopPropagation();

    },
  }
}
</script>

<style lang="stylus" scoped>
// .yo-scroller
  // height 100%
.yo-scroller-wrapper
  height 100%
  -webkit-overflow-scrolling touch
  overflow auto
  position relative
</style>