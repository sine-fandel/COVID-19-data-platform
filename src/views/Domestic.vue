<template>
<div>
  <div class="data-summary">
    <h3>Domestic data</h3>
    <div class="data-summary-row-1">
      <div class="data-summary-current" style="width: 100px; height: 50px; margin-right: 30px; display: inline-block; ">
        current cases
        <div style="color: #ff6a57; font-size: 1.3125rem; font-family: PingFangSC-Medium;">{{ case_data.current_data }}</div>
      </div>
      <div class="data-summary-import" style="width: 100px; height: 50px; margin-left: 30px; display: inline-block; ">
        import cases
        <div style="color: #ec9217; font-size: 1.3125rem; font-family: PingFangSC-Medium;">{{ case_data.import_data }}</div>
        </div>
      <div class="data-summary-severe" style="width: 100px; height: 50px; margin-left: 60px; display: inline-block; ">
        severe cases
        <div style="color: #545499; font-size: 1.3125rem; font-family: PingFangSC-Medium;">{{ case_data.severe_data }}</div>
        </div>
    </div>
    <div class="data-summary-row-2">
      <div class="data-summary-overall" style="width: 100px; height: 50px; margin-right: 30px; margin-top: 10px; display: inline-block; ">
        overall cases
        <div style="color: #e83132; font-size: 1.3125rem; font-family: PingFangSC-Medium;">{{ case_data.overall_data }}</div>
        </div>
      <div class="data-summary-cure" style="width: 100px; height: 50px; margin-left: 30px; display: inline-block; ">
        cured cases
        <div style="color: #10aeb5; font-size: 1.3125rem; font-family: PingFangSC-Medium;">{{ case_data.cured_data }}</div>
        </div>
      <div class="data-summary-death" style="width: 100px; height: 50px; margin-left: 60px; display: inline-block;">
        death cases
        <div style="color: #4d5054; font-size: 1.3125rem; font-family: PingFangSC-Medium;">{{ case_data.death_data }}</div>
        </div>
    </div>
  </div>

  <div style="border-radius: .625rem; background-color: #f8f9fa; margin-bottom: 30px">
    <el-tabs v-model="activeName" @tab-click="handleClick" style="margin-top: 60px;" tab-position="top" stretch="true">
      <el-tab-pane label="currently cases" name="first">
        <div ref="mapbox" class="map" style="height:500px;width:600px; margin-left:140px;"></div>
      </el-tab-pane>
      <el-tab-pane label="total cases" name="second">
        <div ref="mapbox1" class="map1" style="height:500px;width:600px; margin-left:140px;"></div>
      </el-tab-pane>
    </el-tabs>
  </div>

  <div style="border-radius: .625rem; background-color: #f8f9fa; margin-bottom: 100px">
    <el-tabs v-model="activeName1" @tab-click="handleClick" style="margin-top: 60px;" tab-position="bottom" stretch="true" type="border-card">
      <el-tab-pane label="newly / total" name="first">
        <div ref="linechart" class="linechart" style="height:400px;width:600px; margin-left: 120px;"></div>
      </el-tab-pane>
      <el-tab-pane label="crue / death" name="second">
        <div ref="linechart1" class="linechart1" style="height:400px;width:600px; margin-left: 120px;"></div>
      </el-tab-pane>
      <el-tab-pane label="newly import / total import" name="third">
        <div ref="linechart2" class="linechart2" style="height:400px;width:600px; margin-left: 120px;"></div>
      </el-tab-pane>
    </el-tabs>
  </div>

  <h3>Details</h3>
  <div class="table">
    <el-table :data="tableData" ref="detailsTable" style="width: 100%; margin-bottom: 60px;" @row-click="showDetails">
      <el-table-column type="expand">
        <template>
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item>
              <province :city="cityList"></province>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column
        prop="ename"
        label="region"
        sortable
        width="100">
      </el-table-column>
      <el-table-column
        prop="conadd"
        label="newly increased"
        sortable
        width="160">
      </el-table-column>
      <el-table-column
        prop="econNum"
        label="currently cases"
        sortable
        width="150">
      </el-table-column>
      <el-table-column
        prop="value"
        label="total cases"
        sortable
        width="140">
      </el-table-column>
      <el-table-column
        prop="cureNum"
        label="cured cases"
        sortable
        width="140">
      </el-table-column>
      <el-table-column
        prop="deathNum"
        label="death cases"
        sortable
        width="140">
      </el-table-column>

    </el-table>
  </div>



</div>
</template>

<script>
import echarts from 'echarts'
import 'echarts/map/js/china'
import 'echarts/map/js/world'
import jsonp from 'jsonp'
import province from "../components/Province"

const option={
    title:{
      text:"epidemic map",
      link:"https://baidu.com",
      subtext:"map",
      sublink:"https://baidu.com"

    },
    series:[{
      name:"cases",
      type:'map', //告诉echarts要去渲染一个地图
      map:'china',
      label:{
        show:true, // 控制对应地区的汉字
        color:'#333',
        fontSize:10,
      },
      roam:true, //控制地图放大缩小
      zoom:1.2 ,//控制地图的放大缩小
      data:[] ,
      itemStyle:{
        areaColor:'#eee',
        borderColor:"green"  //边框颜色
      },
      emphasis:{
         label:{
           color:'#fff',
           fontsize:12
         },
        itemStyle: {
           areaColor: '#83b5e7'
        }
      }
    }
    ],
    visualMap:[{
      type:'piecewise',
      show:true,
      // splitNumber:4,
      pieces:[
          //分段
        {min:10000},
        {min:1000,max:9990},
        {min:100,max:999},
        {min:10,max:99},
        {min:1,max:9},

      ],
      inRange:{
        symbol:'rect',
        color:['#ffc0b1','#9c0505']
      },
      itemWidth:20,
      itemHeight:10
    }],
    tooltip:{
      trigger:'item'  //鼠标移入后显示人数
    }
  }

  var linec = {
           
              title: { //图表标题，可以通过show:true/false控制显示与否，subtext:'二级标题',
                text: 'line chart',
                subtext: 'newly / total'
            },
            backgroundColor: '#FFFFFF',
            
            tooltip : {//鼠标浮动时的工具条，显示鼠标所在区域的数据，trigger这个地方每种图有不同的设置
                trigger: 'axis'
            },
            legend: {// 图例，每条折线或者项对应的示例
                data:[]
            },
            calculable : true,
            xAxis : [
                {
                    axisLabel:{
                        rotate: 30,
                        interval: 7
                    },
                    axisLine:{
                        lineStyle :{
                            color: '#CCCCCC'
                        }
                    },
                    type : 'category',
                    boundaryGap : false,
                    minInterval: 1,
                    data: []
                },
            ],
            yAxis : [
                {
                    type : 'value',
                    axisLine:{
                        lineStyle :{
                            color: '#CCCCCC'
                        }
                    }
                }
            ],
            series : [
                {
                    name:'newly comfirm',
                    type:'line',
                    // symbol:'none',//原点
                    smooth: 0.2,//弧度
                    color:['#66AEDE'],
                    // data:Y轴数据
                    data: []
                },
                {
                    name:'total cases',
                    type:'line',
                    smooth: 0.2,//弧度
                    color:['#DC143C'],
                    data: []
                },
            ]
        };
    var linec1 = {
           
              title: { //图表标题，可以通过show:true/false控制显示与否，subtext:'二级标题',
                text: 'line chart',
                subtext: 'cure / death'
            },
            backgroundColor: '#FFFFFF',
            
            tooltip : {//鼠标浮动时的工具条，显示鼠标所在区域的数据，trigger这个地方每种图有不同的设置
                trigger: 'axis'
            },
            legend: {// 图例，每条折线或者项对应的示例
                data:[]
            },
            calculable : true,
            xAxis : [
                {
                    axisLabel:{
                        rotate: 30,
                        interval: 7
                    },
                    axisLine:{
                        lineStyle :{
                            color: '#CCCCCC'
                        }
                    },
                    type : 'category',
                    boundaryGap : false,
                    minInterval: 1,
                    data: []
                },
            ],
            yAxis : [
                {
                    type : 'value',
                    axisLine:{
                        lineStyle :{
                            color: '#CCCCCC'
                        }
                    }
                }
            ],
            series : [
                {
                    name:'cured',
                    type:'line',
                    // symbol:'none',//原点
                    smooth: 0.2,//弧度
                    color:['#67C23A'],
                    // data:Y轴数据
                    data: []
                },
                {
                    name:'death',
                    type:'line',
                    smooth: 0.2,//弧度
                    color:['#606266'],
                    data: []
                },
            ]
        };
      var linec2 = {
              title: { //图表标题，可以通过show:true/false控制显示与否，subtext:'二级标题',
                text: 'line chart',
                subtext: 'newly import / total import'
            },
            backgroundColor: '#FFFFFF',
            
            tooltip : {//鼠标浮动时的工具条，显示鼠标所在区域的数据，trigger这个地方每种图有不同的设置
                trigger: 'axis'
            },
            legend: {// 图例，每条折线或者项对应的示例
                data:[]
            },
            calculable : true,
            xAxis : [
                {
                    axisLabel:{
                        rotate: 30,
                        interval: 7
                    },
                    axisLine:{
                        lineStyle :{
                            color: '#CCCCCC'
                        }
                    },
                    type : 'category',
                    boundaryGap : false,
                    minInterval: 1,
                    data: []
                },
            ],
            yAxis : [
                {
                    type : 'value',
                    axisLine:{
                        lineStyle :{
                            color: '#CCCCCC'
                        }
                    }
                }
            ],
            series : [
                {
                    name:'newly import',
                    type:'line',
                    smooth: 0.2,//弧度
                    color:['#FF79BC'],
                    // data:Y轴数据
                    data: []
                },
                {
                    name:'total import',
                    type:'line',
                    smooth: 0.2,//弧度
                    color:['#005757'],
                    data: []
                },
            ]
        };


export default {
  name: "Domestic",
  data () {
    return {
      case_data: {
        current_data: '',
        import_data: '',
        severe_data: '',
        overall_data: '',
        cured_data: '',
        death_data: '',
      },
      whichMap: false,
      activeName: "first",
      activeName1: "first",
      tableData: [],
      proList: {},
      cityList: [],
      tableHead: [],
      list: [],
      expand: false,
    }
  },
  mounted(){  //template挂载到页面时调用
    this.getData(); //执行getData方法
    this.mychart = echarts.init(this.$refs.mapbox);
    this.mychart.setOption(option)
    this.mychart1 = echarts.init(this.$refs.mapbox1);
    this.mychart1.setOption(option)
    this.getlineChart ();
    this.linechart = echarts.init(this.$refs.linechart);
    this.linechart.setOption(linec);
    this.linechart1 = echarts.init(this.$refs.linechart1);
    this.linechart1.setOption(linec1)
    this.linechart2 = echarts.init(this.$refs.linechart2);
    this.linechart2.setOption(linec2)
  },
  methods: {
    getData(){
      jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427', //接口
          {},
          (err,data)=>{
            if(!err){
              //代表请求数据成功
              console.log(data)
              // get the summary data
              this.case_data.current_data = data.data.econNum;
              this.case_data.import_data = data.data.jwsrNum;
              this.case_data.severe_data = data.data.heconNum;
              this.case_data.overall_data = data.data.gntotal;
              this.case_data.cured_data = data.data.curetotal;
              this.case_data.death_data = data.data.deathtotal;

              // get the map data
              let list = data.data.list.map (item=> ({name: item.name, value: item.econNum}))   //从接口获取到数据后，使用map()函数，进行接口数据映射
              option.series[0].data = list;
              this.mychart.setOption (option) //这行代码能执行的前提是DOM已经渲染完成，只有DOM已渲染完成才能echarts初

              let list1 = data.data.list.map (item=> ({name: item.name, value: item.value}))   //从接口获取到数据后，使用map()函数，进行接口数据映射
              option.series[0].data = list1;
              this.mychart1.setOption (option) //这行代码能执行的前提是DOM已经渲染完成，只有DOM已渲染完成才能echarts
  
              // get the table data
              this.tableData = data.data.list;
              for (let i = 0; i < 34; i++) {
                this.proList[data.data.list[i].ename] = data.data.list[i].city;  // city data
              }
              console.log (this.proList);
              
            }
          })
      },
    getlineChart () {
      jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427',
          {},
          (err,data)=>{
            if(!err){
              let line_list = data.data.historylist.map (item => ({date: item.date, add: item.cn_conadd, total: item.cn_conNum}));
              let j = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec.xAxis[0].data[j] = line_list[i].date;
                j --;
              }
              let k = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec.series[0].data[k] = line_list[i].add;
                k --;
              }
              let s = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec.series[1].data[s] = line_list[i].total;
                s --;
              }
              this.linechart.setOption(linec);

              let line_list1 = data.data.historylist.map (item => ({date: item.date, cure: item.cn_cureNum, death: item.cn_deathNum}));
              let jj = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec1.xAxis[0].data[jj] = line_list1[i].date;
                jj --;
              }
              let kk = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec1.series[0].data[kk] = line_list1[i].cure;
                kk --;
              }
              let ss = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec1.series[1].data[ss] = line_list1[i].death;
                ss --;
              }
              this.linechart1.setOption(linec1);

              let line_list2 = data.data.historylist.map (item => ({date: item.date, addjwsr: item.cn_addjwsrNum, jwsr: item.cn_jwsrNum}));
              let jjj = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec2.xAxis[0].data[jjj] = line_list2[i].date;
                jjj --;
              }
              let kkk = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec2.series[0].data[kkk] = line_list2[i].addjwsr;
                kkk --;
              }
              let sss = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec2.series[1].data[sss] = line_list2[i].jwsr;
                sss --;
              }
              this.linechart2.setOption(linec2);
            }
          })
    },
    showDetails (row) {
      this.expand = !this.expand;
      this.$refs.detailsTable.toggleRowExpansion(row, this.expand);
      this.cityList = this.proList[row.ename];
      this.pro = row.name;
      console.log (this.cityList);
    }
  },
  components: {
    province
  }

}
</script>

<style scoped>
.map {
  
}
</style>