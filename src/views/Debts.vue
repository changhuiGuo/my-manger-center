<template>
  <div>
    <pie-charts :chartData="pieChartData" chartMode="debts" :period="period"></pie-charts>
    <trend-charts :chartData="trendChartData" chartMode="debts"></trend-charts>
    <collapsed-table :listData="debtsData" chartMode="debts"></collapsed-table>
  </div>
</template>

<script>
import {mapState,mapMutations,mapActions} from 'vuex'
import api from '@/common/api'
import pieCharts from '@/components/PieCharts'
import trendCharts from '@/components/TrendCharts'
import collapsedList from '@/components/CollapsedList'
import collapsedTable from '@/components/CollapsedTable'
export default {
  name: 'debts',
  data() {
    return {
      debtsData: [],
      period: ''
    }
  },
  components:{
    trendCharts,
    pieCharts,
    collapsedList,
    collapsedTable
  },
  created(){
    api.getDebtsData().then(res=>{
      if(res.data.length){
        this.debtsData = res.data.map(item=>{
          item['总计'] = 0;
          for(var key in item){
            if(typeof(item[key])==='number'&&key!='总计'){
              item['总计'] = parseFloat((item['总计']+item[key]).toFixed(2))
            }
          }
          return item
        })
        this.period = this.debtsData[this.debtsData.length-1]['月份'] + ',' + this.debtsData[0]['月份']
      }
    })
  },
  mounted(){
  },
  computed: {
    pieChartData(){
      let temp = [];
      for(var key in this.debtsData[0]){
        if(typeof(this.debtsData[0][key])=="number"&&key!='总计'&&this.debtsData[0][key]>0){
          temp.push({a: '1',name: key,money: this.debtsData[0][key]});
        }
      }
      let map = {}, total = 0;
      temp.forEach(item=>{
        map[item.name] = '¥ ' + item.money;
        total += item.money;
      });
      return  {data: temp,map: map,total: total}
    },
    trendChartData(){
      let temp = []
      this.debtsData.forEach(item=>{
        for(var key in item){
          if(typeof(item[key])=="number"){
            temp.push({value: item[key],name: key,date: item['月份']})
          }
        }
      })
      return temp
    },
    collapseData(){
      let temp = [];
      this.debtsData.forEach(item=>{
      })
      return temp
    }
  },
  methods: {
    ...mapMutations([
      'setdebtsData'
    ])
  }
}
</script>

<style lang="less">
  
</style>