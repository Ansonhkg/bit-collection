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
    <button @click="shortcuts_1()" style="margin-right: 5px;">Shortcut 1</button>
    <button @click="shortcuts_2()">Shortcut 2</button>
    <DateRangePicker 
      :shortcuts="shortcuts"
      :demo="true"
    />
    <!-- <DRP/> -->

  </div>
</template>

<script>
import DateRangePicker from './components/DateRangePicker/DateRangePicker';
// import DRP from '@bit/ansonhkg.collection.faros-date-range-picker';

export default {
  data(){
    return {
      shortcuts: [
        {
          title: 'FOO - SHORTCUT 1',
          number: 3,
          unit: 'days'
        },
        {
          title: 'FOO - SHORTCUT 2',
          number: 3,
          unit: 'days'
        }
      ]
    } 
  },
  components:{
    DateRangePicker,
    // DRP
  },
  methods:{
    shortcuts_1(){
      this.shortcuts = [
        {
          title: 'FOO - SHORTCUT 1',
          number: 3,
          unit: 'days'
        },
        {
          title: 'FOO - SHORTCUT 2',
          number: 3,
          unit: 'days'
        }
      ]
    },
    shortcuts_2(){
      this.shortcuts = [
        {
          title: 'BAR - SHORTCUT 2',
          number: 5,
          unit: 'days'
        }
      ]
    }
  }
}
</script>

<style>

</style>
