<template>
  <div
    style="display: flex; width: 100vw; height: 100vh; justify-content: center"
  >
    <div id="main" style="width: 800px; height: 600px"></div>
  </div>
  <div style="position: fixed; top: 10px; left: 20px">
    <el-select
      v-model="value"
      placeholder="请选择"
      multiple
      filterable
      @change="change"
    >
      <el-option
        v-for="item in cities"
        :key="item.value"
        :label="item.label"
        :value="item.value"
      />
    </el-select>
  </div>
</template>

<script>
import * as echarts from "echarts";
import "../assets/china.js";
import citysData from "./city.json";
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      geoObject: {},
      data: [],
      value: [],
      cities: [
        // {
        //   value: "Beijing",
        //   label: "Beijing",
        // },
      ],
    };
  },
  created() {
    let ss = {};
    this.cities = [];
    citysData.forEach((it) => {
      ss[it.name] = [Number(it.longitude), Number(it.latitude)];
          this.cities.push({
            label: it.name,
            value: it.name,
          });
    });
    this.geoObject = ss;
  },
  methods: {
    init() {
      var chartDom = document.getElementById("main");
      var myChart = echarts.init(chartDom);
      var convertData = (data) => {
        var res = [];
        for (var i = 0; i < data.length; i++) {
          var geoCoord = this.geoObject[data[i].name];
          if (geoCoord) {
            res.push({
              name: data[i].name,
              value: geoCoord.concat(data[i].value),
            });
          }
        }
        return res;
      };
      var option = {
        backgroundColor: "#404a59",
        title: {
          text: "",
          left: "center",
          textStyle: {
            color: "#fff",
          },
        },
        tooltip: {
          trigger: "item",
        },
        legend: {
          orient: "vertical",
          y: "bottom",
          x: "right",
          data: ["haidilao"],
          textStyle: {
            color: "#fff",
          },
        },
        geo: {
          map: "china",
          label: {
            normal: {
              show: true,
              color: "#575859",
            },
            emphasis: {
              show: false,
            },
          },
          roam: true,
          itemStyle: {
            normal: {
              areaColor: "#323c48",
              borderColor: "#111",
            },
            emphasis: {
              areaColor: "#2a333d",
            },
          },
        },
        series: [
          {
            name: "地理位置+数量",
            type: "scatter",
            coordinateSystem: "geo",
            data: convertData(this.data),

            symbolSize: function (val) {
              return val[1] / 8;
            },
            label: {
              normal: {
                formatter: "{b}",
                position: "right",
              },
              emphasis: {
                show: true,
              },
            },
            itemStyle: {
              normal: {
                color: "#ddb926",
              },
            },
          },
          {
            name: "",
            type: "effectScatter",
            coordinateSystem: "geo",
            data: convertData(
              this.data
                .sort(function (a, b) {
                  return b.value - a.value;
                })
                .slice(0, 6)
            ),
            symbolSize: function (val) {
              return val[1] / 5;
            },
            showEffectOn: "render",
            rippleEffect: {
              brushType: "stroke",
            },
            hoverAnimation: true,
            label: {
              normal: {
                formatter: "{b}",
                position: "right",
                show: true,
              },
            },
            itemStyle: {
              normal: {
                color: "#f4e925",
                shadowBlur: 10,
                shadowColor: "#333",
              },
            },
            zlevel: 1,
          },
        ],
      };
      myChart.setOption(option);
    },
    change(val) {
      this.data = val.map((it) => ({
        name: it,
        value: 14,
      }));
      this.init();
    },
  },
  mounted() {
    this.init();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
