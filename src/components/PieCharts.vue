<template>
  <div>
    <title-bar name="总览" tips="数据截至：2018年5月 - 2020年1月" :hr="false"></title-bar>
    <div class="chart-content">
      <canvas id="pieChart" width="360" :height="chartMode==='income'?180:186"></canvas> 
    </div>
  </div>
</template>

<script>
import {mapState,mapMutations,mapActions} from 'vuex'
import F2 from '@antv/f2'
import titleBar from '@/components/TitleBar'
export default {
  name: 'pieCharts',
  props: ['chartData','chartMode'],
  data() {
    return {
    }
  },
  created(){
  },
  mounted(){
  },
  watch:{
    chartData(val){
      this.$nextTick(()=>{
        this.initPieCharts()
      })
    }
  },
  computed: {
  },
  components: {
    titleBar
  },
  methods: {
    initPieCharts(){
      const chart = new F2.Chart({
        id: 'pieChart',
        pixelRatio: window.devicePixelRatio,
        padding: [ 20, 'auto' ]
      });
      chart.source(this.chartData.data);
      chart.tooltip(false);
      chart.legend({
        position: 'right',
        clickable: false,
        itemMarginBottom: 10,
        itemFormatter:  (val) => {
          return val + '    ' + this.chartData.map[val];
        }
      });
      chart.coord('polar', {
        transposed: true,
        innerRadius: 0.7,
        radius: 0.85
      });
      chart.axis(false);
      chart.interval()
        .position('a*money')
        .color('name', [ '#F04864','#13C2C2','#2FC25B','#FACC14','#1890FF' ])
        .adjust('stack');

      chart.guide().html({
        position: [ '50%', '45%' ],
        html: `<div style="width: 250px;height: 40px;text-align: center;">
            <div style="font-size: 14px">总计</div>
            <div style="font-size: 18px">¥${this.chartData.total.toFixed(0)}</div>
          </div>`
      });
      chart.render();
    },
  }
}
</script>

<style lang="less">
  .chart-content{
    text-align: center;
    background: #fff;
  }
</style>