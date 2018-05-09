<template>
  <div>
    home - {{test}}

    <YoButton @click.native="showPopupFn">showPopup</YoButton>

    <YoPopup
      :show-mask="false"
      :show="showPopup">
      lalalla
    </YoPopup>
  </div>
</template>

<script>

import {
  YoButton,
  YoPopup,
  YoScroller,
  YoSlider,
  YoTab
} from '@/../index';

export default {

  name: 'Home',

  components: {
    YoButton,
    YoPopup,
    YoScroller,
    YoSlider,
    YoTab
  },

  data() {
    return {
      test: 123,
      showPopup: false,
    };
  },

  methods: {
    showPopupFn() {
      this.showPopup = false;
      this.$nextTick(()=> {
        this.showPopup = true;
      });
    }
  }
};
</script>

<style lang="stylus" scoped>
</style>