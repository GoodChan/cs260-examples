<template>
  <div>
    <select v-model="monthText" name="month">
      <option value="0">January</option>
      <option value="1">February</option>
      <option value="2">March</option>
      <option value="3">April</option>
      <option value="4">May</option>
      <option value="5">June</option>
      <option value="6">July</option>
      <option value="7">August</option>
      <option value="8">September</option>
      <option value="9">October</option>
      <option value="10">November</option>
      <option value="11">December</option>
    </select>
    <div class="graph">
      <bar-chart v-if="chartData" :chart-data="chartData" :options="options" />
    </div>
  </div>
</template>

<script>
 import BarChart from './BarChart.js';
 export default {
   components: {BarChart},
   name: 'Graph',
   data () {
     return {
       monthText: '1',
       options: {
	 responsive: true,
	 maintainAspectRatio: true,
	 scales: {
	   yAxes: [{
	     barPercentage:0.5,
	   }],
	 }
       },
     }
   },
   created: function() {
     this.getItems();
   },
   computed: {
     items: function() {
       return this.$store.getters.items;
     },
     month: function() {
       return parseInt(this.monthText);
     },
     chartData: function() {
       let labels = [];
       let data = [];
       // sort by date
       this.items.sort(function(a,b) {
	 return b.completedDate - a.completedDate;
       });
       // setup data
       let completedCounts = {}
       for (let day = 1; day < 32; day++ ) {
	 completedCounts[day] = 0;
       }
       this.items.forEach(item => {
	 let day = new Date(item.completedDate).getDate();
	 let month = new Date(item.completedDate).getMonth();
	 if (month === this.month && item.completed)
	   completedCounts[day] += 1;
       });
       for (let day in completedCounts) {
	 labels.push(day.toString());
	 data.push(completedCounts[day]);
       };
       return {
	 labels:labels,
	 datasets: [
	   {
	     label: 'Completed Tasks per Day',
	     backgroundColor: '#f87979',
	     data: data,
	   }
	 ]
       }
     },
   },
   methods: {
     getItems: function() {
       this.$store.dispatch('getItems');
     },
   }
 }
</script>

<style scoped>
 .graph {
     position: relative;
     width: 800px;
 }
</style>
