<template>

  <div class="bar-chart" v-model="charResize" ref="myEchart"></div>

</template>

<script>
  import store from '../../../store/index'

  export default {
    name: "bar-chart",
    props: {
      dataArray: Array,
      xField: String|Number,
      yField: String|Number,
      xName: String,
      yName: String,
      xFormatter: String|Function,
      yFormatter: String|Function,
      valueMax: String|Number,
    },
    watch: {
      dataArray: function (val) {
        this.chartOption.dataset.source = val;
        this.updateChart();
      }
    },
    data() {
      return {
        chart: null,
        chartTrue: false,
        chartOption: {
          // backgroundColor: "#2c343c",
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'shadow',
              label: {
                backgroundColor: '#8c7cde'
              }
            },
          },
          dataset: {
            source: this.dataArray
          },
          xAxis: {
            name: this.xName,
            type: 'category',
            boundaryGap: true,
            axisLabel: {
              formatter: this.xFormatter,
              color: '#666',
              fontSize: 12
            },
            axisTick: {
              alignWithLabel: true
            }
            //data: ['2', '4', '6', '8', '10', '12', '14']
          },
          yAxis: {
            name: this.yName,
            type: 'value',
            axisLine: {
              lineStyle: {
                color: '#666'
              }
            },
            nameTextStyle: {
              color: '#666',
              fontSize: 12
            },
            min: 0,
            max: this.valueMax,
            axisLabel: {
              formatter: this.yFormatter,
              color: '#666',
              fontSize: 12
            }
          },
          series: [{
            //data: [1, 4, 6, 5, 8, 3, 2],
            encode: {
              x: this.xField,
              y: this.yField
            },
            type: 'bar',
            barWidth: 10,
            itemStyle: {
              normal: {
                color: new this.$echarts.graphic.LinearGradient(
                  0, 0, 0, 1, [{
                    offset: 0,
                    color: '#92bbfb'
                  },
                    {
                      offset: 0.8,
                      color: '#9c9afd'
                    }
                  ],
                  false
                )
              }
            }
          }]
        },
        screenWidth: '',
      }
    },
    mounted() {
      this.initChart();
    },
    computed: {
      charResize() {
        if (this.chartTrue) {
          this.chart.resize();
        }
        return store.state.vuexWidth
      }
    },
    methods: {
      //表格加载
      initChart() {
        this.chart = this.$echarts.init(this.$refs.myEchart);
        // 把配置和数据放这里
        this.chart.setOption(this.chartOption);
        this.chartTrue = true
      },
      updateChart() {
        // console.log('更新 bar chart: ');
        this.chart.setOption({ dataset: { source: this.dataArray } });
      }
    }
  }
</script>

<style scoped lang="scss">
  .bar-chart {
    position: absolute;
    height: 100%;
    width: 100%;
  }
</style>
