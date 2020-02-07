<template>
  <div>
    <pie-charts :chartData="pieChartData" chartMode="income"></pie-charts>
    <trend-charts :chartData="trendChartData" chartMode="income"></trend-charts>
    <collapsed-list :listData="collapseData" chartMode="income"></collapsed-list>
    <collapsed-table :listData="incomeData" chartMode="income"></collapsed-table>
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
  name: 'inCome',
  data() {
    return {
      incomeData: []
    }
  },
  components:{
    trendCharts,
    pieCharts,
    collapsedList,
    collapsedTable
  },
  created(){
    api.getIncomeData().then(res=>{
      if(res.data.length){
        this.incomeData = res.data.map(item=>{
          item['总计'] = 0;
          for(var key in item){
            if(typeof(item[key])==='number'&&key!='总计'){
              item['总计'] = parseFloat((item['总计']+item[key]).toFixed(2))
            }
          }
          return item
        })
      }
    })
  },
  mounted(){
  },
  computed: {
    pieChartData(){
      let temp = [];
      this.incomeData.forEach(item=>{
        for(var key in item){
          if(typeof(item[key])=="number"&&key!='总计'){
            let isPush = true;
            temp.forEach(sitem=>{
              if(sitem.name===key){
                isPush = false;
                sitem.money = parseFloat((sitem.money+item[key]).toFixed(2))
              }
            })
            isPush ? temp.push({a: '1',name: key,money: item[key]}) : '';
          }
        }
      })
      let map = {}, total = 0;
      temp.forEach(item=>{
        map[item.name] = '¥ ' + item.money;
        total += item.money;
      });
      return  {data: temp,map: map,total: total}
    },
    trendChartData(){
      let temp = []
      this.incomeData.forEach(item=>{
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
      this.incomeData.forEach(item=>{
        let year = item['月份'].substring(0,4);
        let pushInx = 0;
        temp.forEach((sitem,sindex)=>{
          if(sitem['年度']===year){
            pushInx = sindex+1
          }
        })
        if(pushInx){
          temp[pushInx-1]['年度'] = year;
          temp[pushInx-1]['补发'] = parseFloat((temp[pushInx-1]['补发'] + item['补发']).toFixed(2));
          temp[pushInx-1]['个人社保'] = parseFloat((temp[pushInx-1]['个人社保'] + item['个人社保']).toFixed(2));
          temp[pushInx-1]['个人公积金'] = parseFloat((temp[pushInx-1]['个人公积金'] + item['个人公积金']).toFixed(2));
          temp[pushInx-1]['个人所得税'] = parseFloat((temp[pushInx-1]['个人所得税'] + item['个人所得税']).toFixed(2));
          temp[pushInx-1]['实发工资'] = parseFloat((temp[pushInx-1]['实发工资'] + item['实发工资']).toFixed(2));
          temp[pushInx-1]['单位'].includes(item['单位']) ? '': temp[pushInx-1]['单位'] += ','+item['单位'];
          temp[pushInx-1]['总计'] = parseFloat((temp[pushInx-1]['补发'] + temp[pushInx-1]['个人社保'] + temp[pushInx-1]['个人公积金'] + temp[pushInx-1]['个人所得税'] + temp[pushInx-1]['实发工资']).toFixed(0));
        }
        else{
          temp.push({
            '年度': year,
            '补发': item['补发'],
            '个人社保': item['个人社保'],
            '个人公积金': item['个人公积金'],
            '个人所得税': item['个人所得税'],
            '实发工资': item['实发工资'],
            '单位': item['单位'],
            '总计': item['总计']
            });
        }
      })
      return temp
    }
  },
  methods: {
    ...mapMutations([
      'setIncomeData'
    ])
  }
}
</script>

<style lang="less">
  
</style>