<template>
  <div id="app">
    <h1>My Collection</h1>
    <ul>
      <li>node-sass</li>
      <li>sass-loader</li>
      <li>moment</li>
      <li>material-design-icons-iconfont</li>
    </ul>
    <hr>
    <h2>Date Range Picker</h2>
    
    <DateRangePicker />
    <!-- <DRP/> -->

  </div>
</template>

<script>
import DateRangePicker from './components/DateRangePicker/DateRangePicker';
// import DRP from '@bit/ansonhkg.collection.faros-date-range-picker';

export default {
  components:{
    DateRangePicker,
    // DRP
  }
}
</script>

<style>

</style>
