<template>
	<MyTemplate :settings="myTemplate">
		
		<!-- demo -->
		<button @click="shortcuts_1()" style="margin-right: 5px;">Shortcut 1</button>
		<button @click="shortcuts_2()">Shortcut 2</button>

		<DateRangePicker 
			:shortcuts="shortcuts"
			:demo="true"
		/>
	</MyTemplate>
</template>

<script>
import MyTemplate from './template';
import DateRangePicker from '../components/DateRangePicker/DateRangePicker';

export default {
  components:{
		DateRangePicker,
		MyTemplate
  },
  data(){
    return {
			// layout settings
			myTemplate:{
				title: 'Date Range Picker',
				dependencies:[
					'node-sass',
					'sass-loader',
					'moment',
					'material-design-icons-iconfront'
				],
			},

			// demo variables
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
