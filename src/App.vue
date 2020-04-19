<template>
  <div id="app">

    <!-- header -->
    <div class="header">
      <h1>My Resuable Components</h1>
      <ul class="list">
        <li class="list-item" v-for="item in registeredComponents" :key="item">
          <div @click="onSelect(item)">{{ item }}</div>
        </li>
      </ul>
    </div>

    <!-- demo area -->
    <div class="demo-wrapper" :class="{'demo-wrapper--active' : currentDemo != ''}">
      <component 
        v-bind:is="currentDemo" 
      />
    </div>
  </div>
</template>

<script>
import DateRangePicker from './demos/DateRangePicker';

export default {
  data(){
    return {
      registeredComponents: [],
      currentDemo: ''
    }
  },
  components:{
    DateRangePicker
  },
  mounted(){
    this.registeredComponents = Object.keys(this.$options.components);
  },
  methods:{
    onSelect(item){
      this.currentDemo = item;
      
    }
  }

}
</script>

<style>
body{
  font-family: sans-serif;
  background: whitesmoke;
  margin: 0;
}
#app{
  /* display: grid;
  grid-template-columns: 200px calc(100% - 200px); */
}
.demo-wrapper{
  margin-left: 200px;
  margin: 10px;
  background: white;
  box-shadow: 0px 0px 10px grey;
  padding: 15px;
  box-sizing: border-box;
  border-radius: 10px;
  transition: 0.3s all linear;
  transform: translateX(-10px);
  opacity: 0;
  margin-left: 260px;
}
.demo-wrapper--active{
  opacity: 1;
  transform: translateX(0);
}
.header{
  background: #2C5154;
  color: white;
  padding: 10px;
  box-sizing: border-box;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  width: 250px;
}
.header h1{
  font-size: 28px;
}
.list{
  list-style-type: none;
  display: flex;
  padding: unset;
}
.list-item{
  cursor: pointer;
  padding: 5px 10px;
  background: #E6DBC0;
  color: black;
  margin-right: 10px;
  border-radius: 3px;
  transition: all linear 0.1s;
}

.list-item:hover{
  background: #CEB282;
}
</style>