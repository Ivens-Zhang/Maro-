<template>
  <div id="container">
    <div style="height: 40px" class="in_chart_select">
        <el-select
          v-model="portName"
          :placeholder="this.$t('port.placeholder')"
          size="mini"
          @change="changePortName"
        >
          <el-option
            v-for="item in allPortsNames"
            :key="item"
            :label="item"
            :value="item"
          >
          </el-option>
        </el-select>
      </div>
    <div id="main"></div>
  </div>
</template>

<script>
import * as echarts from "echarts";

export default {
  props: {
    chartData: Array,
    legend: Array,
    allPortsNames: Array,
    resDate: String
    // id: String,
  },
  watch: {
    count: function (oldValue, newValue) {
      this.portChart.resize()
    },
    chartData: function (oldValue, newValue) {
      let fulfillmentArr = [];
      let shortageArr = [];
      newValue.forEach((item) => {
        fulfillmentArr.push(item.singlePortFulfillmentTickCount);
        shortageArr.push(item.singlePortShortageTickCount);
      });
      this.option.series[0].data = fulfillmentArr;
      this.option.series[1].data = shortageArr;

      this.option.title.subtext = `${this.resDate} \n${this.$t('port.todayFulfillment')}: ${oldValue[oldValue.length - 1].singlePortFulfillmentTickCount}       ${this.$t('port.todayShortage')}: ${oldValue[oldValue.length - 1].singlePortShortageTickCount}`
      // console.log(fulfillmentArr, shortageArr, 'fffffffff');
      this.portChart.setOption(this.option);
    },
    legend: function (oldValue, newValue) {
      this.option.legend.data = newValue;
      this.portChart.setOption(this.option);
    },
  },
  data() {
    return {
      portChart: null,
      portName: '',
      count: 1,
      option: {
        title: {
          subtext: ` \n${this.$t('port.todayFulfillment')}:           ${this.$t('port.todayShortage')}: `,
          left: '13%',
          bottom: '7%',
          subtextStyle: {
            color: 'white',
            fontSize: '15',
            verticalAlign: 'top',
            lineHeight: 25,
          }
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            label: {
              backgroundColor: "#6a7985",
              formatter: params => {
                return Math.round(params.value)
              }
            },
          },
        },
        legend: {
          data: this.legend,
          right: "10%",
          top: "auto",
          textStyle: {
            color: 'white'
          }
        },
        grid: {
          left: "3%",
          top: "13%",
          containLabel: true,
        },
        xAxis: {
          type: "category"
        },
        yAxis: [
          {
            type: "value",
          },
        ],
        series: [
          {
            name: this.legend[0], // 注意，这里把两个 series 对应的 name 写死了，分别是 legend 的第 0、1 项
            type: "line",
            stack: "总量",
            areaStyle: {},
            emphasis: {
              focus: "series",
            },
            data: [],
          },
          {
            name: this.legend[1],
            type: "line",
            stack: "总量",
            areaStyle: {},
            emphasis: {
              focus: "series",
            },
            data: [],
          },
        ],
      },
    };
  },
  methods: {
    changePortName (data) {
      this.$emit('changePortName', data)
    },
    renderChart(id) {
      this.portChart = echarts.init(document.getElementById(id));
      this.portChart.setOption(this.option);
    },
  },
  mounted() {
    this.$nextTick(function () {
      this.renderChart('main');
    });
  },
};
</script>

<style scoped>
#main {
  min-height: 250px;
  width: 100%;
}
</style>