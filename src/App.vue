<template>
  <div id="app">

    
    <component v-bind:is="'DateRangePicker'"></component>
  </div>
</template>

<script>
import DateRangePicker from './demos/DateRangePicker';

export default {
  data(){

  },
  components:{
    DateRangePicker
  },

}
</script>

<style>

</style>
