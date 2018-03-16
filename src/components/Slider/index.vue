<template>
  <div class="yo-slider" :style="{
    height: `${sliderHeight}px`
  }">
    <div v-if="sliders.length>0 && !ready" class="slider-wrapper">
      <div class="slider-item">
        <!-- <slot :slider="sliders[0]"></slot> -->
      </div>
    </div>
    <div v-if="ready" class="slider-wrapper" :ref="refId" :style="{
      width: `${sliderWidth}px`
    }">
      <div v-if="sliders.length>0" class="slider-item slider-item-clone">
        <slot :slider="sliders.slice(-1)[0]"></slot>
      </div>
      <div class="slider-item" v-for="(item, index) in sliders">
        <slot :slider="item"></slot>
      </div>
      <div v-if="sliders.length>0" class="slider-item slider-item-clone">
        <slot :slider="sliders[0]"></slot>
      </div>
    </div>
  </div>
</template>

<script>

import Hammer from 'hammerjs'
import dynamics from 'dynamics.js'
export default {

  name: 'Slider',

  props: {
    ratio: {
      type: Number
    },
    sliders: {
      type: Array,
      default: ()=>{
        return []
      }
    },

    activeScreen: {
      type: Number,
      // default: 
    },

    onSlider: {
      type: Function
    },

    onComplete: {
      type: Function,
      default: ()=>{
        console.log('onComplete');
      }
    },

    autoplay: {
      type: Boolean,
      default: false
    },

    showPagination: {
      type: Boolean,
      default: false
    },

    lockX: {
      type: Boolean,
      default: false
    },

    lockY: {
      type: Boolean,
      default: false
    }

  },

  data () {
    return {
      ready: false,
      SlideIdleTimeout: null,
      AutoplayInterval: null,
      refId: 'sliderWrapper' + (+ new Date()),
      innerActiveSlide: 0
    }
  },

  computed: {
    width() {
      return document.documentElement.clientWidth
    },

    sliderHeight() {
      if(this.ratio){
        return document.documentElement.clientWidth / this.ratio;
      }
      return 160;
    },
    sliderWidth() {
      if(this.sliders.length){
        return document.documentElement.clientWidth * (this.sliders.length+2)
      }
      return document.documentElement.clientWidth * 3
    }
  },
  watch: {
    activeScreen(n, o) {
      this.$nextTick(() => {
        this.slideTo();
        clearTimeout(this.SlideIdleTimeout);
        if(n != 0){
          this.SlideIdleTimeout = setTimeout(()=>{
            this.setActive(0)
          }, 60 * 1000 * 5);
        }
      })
    },

  },

  methods: {

    initTouchMove() {
      const $slider = this.$refs[this.refId];
      const slider = new Hammer($slider);

      slider.get('pan').set({
        threshold: 0
      });

      let startCenter = null;

      let x = 0,
        y = 0;

      slider.on('panmove', (e) => {
        try{
          if (this.sliders.length == 1) {
            return
          }
          if (!startCenter) {
            startCenter = e.center;
          }
          if(!this.lockX){
            x = e.center.x - startCenter.x;
          }
          let index = typeof this.activeScreen == 'undefined' ? this.innerActiveSlide : this.activeScreen;
          Object.assign($slider.style, {
            transform: `translate3d(${x + (index + 1) * -(this.width)}px, 0,0)`
          })
        }catch(err){
          // alert(JSON.stringify(err))
        }
        })
        .on('panend', (e) => {
          try{
            this.resetAutoplay();
            let index = typeof this.activeScreen == 'undefined' ? this.innerActiveSlide : this.activeScreen;

            if (this.sliders.length == 1) {
              return
            }
            if(!this.lockX){
              x = e.center.x - startCenter.x;
            }
            if (x > 0) {
              // to right
              if (x / (this.width) > 0.2) {

                this.setActive(index - 1)
              }
            } else {
              // to left
              if (-x / (this.width) > 0.2) {
                this.setActive(index + 1);
              }
            }
            this.slideTo();
            startCenter = null;
          }catch(err){
            // alert(JSON.stringify(err))
          }
        })

    },

    slideTo(idx) {
      const $slider = this.$refs[this.refId];
      let index = typeof this.activeScreen == 'undefined' ? this.innerActiveSlide : this.activeScreen;

      dynamics.animate($slider, {
        translateX: -(this.width) * (index + 1)
      }, {
        type: dynamics.spring,
        duration: 500,
        frequency: 200,
        friction: 400,
        complete: () => {
          try {
            if (index < 0) {
              this.setActive(this.sliders.length-1);
              $slider.style.transform = `translate3d(${-(this.width) * (this.sliders.length)}px, 0, 0)`
            }
            if (index >= this.sliders.length) {
              this.setActive(0);
              $slider.style.transform = `translate3d(${-(this.width) * (0 + 1)}px, 0, 0)`
            }
          } catch (err) {
            console.log(err);
          }

        }
      })
    },

    setActive(idx){
      if(this.onSlider){
        this.onSlider(idx);
      } else{
        this.innerActiveSlide = idx;
      }
    },

    startAutoplay() {
      clearInterval(this.AutoplayInterval);
      
      this.AutoplayInterval = setInterval(()=>{
        let tmpIndex = this.activeScreen||this.innerActiveSlide;
        tmpIndex += 1;
        if(tmpIndex>=this.sliders.length){
          tmpIndex = 0;
        }
        this.innerActiveSlide = tmpIndex;
        this.slideTo();
      }, 5000)
    },

    resetAutoplay() {
      if(this.autoplay){

      clearInterval(this.AutoplayInterval);
        setTimeout(()=>{
          this.startAutoplay();
        }, 1000 * 10)
      }
    }
  },

  mounted() {
    this.$nextTick(()=>{
      this.ready = true;
      this.$nextTick(()=>{
        this.initTouchMove();
        this.slideTo();
        if(this.autoplay){
          this.startAutoplay();
        }
      })
    })
  }

}
</script>

<style lang="stylus" scoped>
.yo-slider
  width 100%
  height 160px
  background-color #fff
.slider-wrapper
  height 100%
  display flex
.slider-item
  flex 1
  position relative 
  overflow auto
</style>