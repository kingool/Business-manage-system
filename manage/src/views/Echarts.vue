<template>
  <div class="ProductEcharts">
    <h4 class="title" v-if="proTypeList == []">您的商店暂无商品请前去添加商品</h4>
    <h4 class="title" v-else>请点击要查询的商品的类型</h4>
    <div class="btn-group" role="toolbar" aria-label="Basic example">
      <button
        type="button"
        class="btn btn-primary"
        v-for="(typeList,index) in proTypeList"
        :key="index"
        @click="changeType(index)"
      >{{typeList.type}}</button>
    </div>
    <div class="eCharts-box">
      <div id="main" :style="{width: '800px', height: '400px'}"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Echarts",
  data() {
    return {
      proTypeList: [],
    };
  },
  created() {},
  mounted() {
    this.getPriType();
  },
  methods: {
    //创建echarts
    getPriType() {
      //获取商品种类
      this.axios({
        method: "GET",
        url: "/proType"
      })
        .then(result => {
          this.proTypeList = result.data.result;
          console.log(this.proTypeList);
        })
        .catch(err => {
          console.log("err ==>", err);
        });
    }, //init结束
    changeType(index) {
      //初始化echarts实例
      let procharts = this.$echarts.init(document.getElementById("main"));

      //指定图标的配置项和数据
      procharts.setOption({
        title: {
          text: "商品数量统计图"
        },
        tooltip: {
          data: "",
        },
        legend: {
          data: ["数量"]
        },
        //X轴
        xAxis: {
          data: []
        },
        yAxis: {},
        //
        series: [
          {
            name: "手机",
            type: "bar",
            data: []
          }
        ]
      }); //结束option对象
      console.log("index ==>", index);
      let condition = {};
      condition.typeId = this.proTypeList[index].typeId;
      this.axios({
        method: "GET",
        url: "/showProduct",
        params: condition
      }).then(result => {
        let proList = result.data.result
        let proName = []
        let proCount = []
        for(let i = 0;i < proList.length;i++){
          proName[i] = proList[i].name
          proCount[i] = proList[i].count
        }
        console.log(proName)
        //异步加载数据
          procharts.setOption({
            xAxis: {
              data: proName
            },
            series: [
              {
                // 根据名字对应到相应的系列
                name: "数量",
                data: proCount
              }
            ]
          });
      }).catch(err => {
        
      })
    }
  }
};
</script>

<style lang="less" scoped>
.title{
  padding: 5px;
}
.eCharts-box {
  margin-top: 30px;
  // #main{
  //   background-color: #666;
  // }
}
</style>>