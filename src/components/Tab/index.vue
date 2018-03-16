<template>
  <div class="yo-tab">
    <div class="yo-tab-item" v-for="(item, index) in tabs"
      :class="{
        active: active == index
      }">
      <div class="yo-tab-item-wrapper"
        @click="select(index)">{{item}}</div>
    </div>
  </div>
</template>

<script>
export default {

  name: 'Tab',

  data () {
    return {
      active: 0
    }
  },

  props: {
    
    onChange: {
      type: Function,
      default: ()=>{

      }
    },
    activeTab: {
      type: Number
    },
    tabs: {
      type: Array
    }
  },

  watch: {
    activeTab(n,o) {
      this.$nextTick(()=>{
        this.active = n;
      })
    }
  },

  methods: {
    select(index) {
      this.active = index;
      this.onChange(index)
    }
  },

  mounted() {
    this.$nextTick(()=>{
      
    })
  }
}
</script>

<style lang="stylus" scoped>
.yo-tab
  display flex
.yo-tab-item
  text-align center
  flex 1
  color #bababa
  padding 10px
  border-bottom 2px solid transparent
  transition all .3s
  &.active
    border-color #ffc629
    .yo-tab-item-wrapper
      transition all .3s
      color #363636
      // background-color #fff

</style>