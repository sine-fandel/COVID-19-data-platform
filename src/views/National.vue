<template>
<div>
  <div class="data-summary">
    <h3>National data</h3>
    <div class="data-summary-row-1">
      <div class="data-summary-current" style="width: 100px; height: 50px; margin-right: 30px; display: inline-block; ">
        current cases
        <div style="color: #ff6a57; font-size: 1.3125rem; font-family: PingFangSC-Medium;">{{ case_data.current_data }}</div>
      </div>
      <div class="data-summary-overall" style="width: 100px; height: 50px; margin-right: 30px; margin-top: 10px; display: inline-block; ">
        overall cases
        <div style="color: #e83132; font-size: 1.3125rem; font-family: PingFangSC-Medium;">{{ case_data.overall_data }}</div>
      </div>
      <div class="data-summary-cure" style="width: 100px; height: 50px; margin-left: 30px; display: inline-block; ">
        cured cases
        <div style="color: #10aeb5; font-size: 1.3125rem; font-family: PingFangSC-Medium;">{{ case_data.cured_data }}</div>
      </div>
      <div class="data-summary-death" style="width: 100px; height: 50px; margin-left: 30px; display: inline-block;">
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
      <el-tab-pane label="total" name="first">
        <div ref="linechart" class="linechart" style="height:400px;width:600px; margin-left: 120px;"></div>
      </el-tab-pane>
      <el-tab-pane label="crue / death" name="second">
        <div ref="linechart1" class="linechart1" style="height:400px;width:600px; margin-left: 120px;"></div>
      </el-tab-pane>
    </el-tabs>
  </div>

  <h3>Detail</h3>
  <div class="table">
    <el-table :data="tableData" ref="detailsTable" style="width: 100%; margin-bottom: 60px;">
      <el-table-column
        prop="name"
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
        sortables
        width="140">
      </el-table-column>
    </el-table>
  </div>
</div>
</template>

<script>
import echarts from 'echarts'
import 'echarts/map/js/world'
import jsonp from 'jsonp'

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
    map:'world',
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
    pieces:[
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
        subtext: 'total'
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
        subtext: 'cure'
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

var nameMap = { "中国": "China", "北马其顿": "Macedonia","英国": "United Kingdom","阿富汗":"Afghanistan", "安哥拉":"Angola", "阿尔巴尼亚":"Albania", "阿尔及利亚":"Algeria", "阿根廷":"Argentina", "亚美尼亚":"Armenia", "澳大利亚" :"Australia", "奥地利":"Austria", "阿塞拜疆":"Azerbaijan", "巴哈马":" Bahamas ", "孟加拉国":"Bangladesh", "比利时":" Belgium ", "贝宁" :"Benin", "布基纳法索":"Burkina Faso", "布隆迪":" Burundi", "保加利亚":"Bulgaria", "波斯尼亚和黑塞哥维那":"Bosnia and Herz", "白俄罗斯" :"Belarus", "伯利兹" :"Belize", "百慕大群岛":"Bermuda", "玻利维亚":"Bolivia", "巴西":"Brazil", "文莱":"Brunei ", "不丹":"Bhutan", "博茨瓦纳":"Botswana", "柬埔寨":"Cambodia", "喀麦隆":"Cameroon", "加拿大":"Canada", "中非共和国":"Central African Rep.", "乍得" :"Chad", "智利" :"Chile", "哥伦比亚":"Colombia", "刚果（金）":"Congo", "哥斯达黎加":"Costa Rica", "科特迪瓦":"Côte d'Ivoire", "克罗地亚" :"Croatia", "古巴" :"Cuba", "塞浦路斯" :"Cyprus", "捷克":"Czech Rep.", "朝鲜" :"Dem.Rep.Korea", "刚果（布）":"Dem. Rep. Congo", "丹麦" :"Denmark", "吉布提":"Djibouti", "多米尼加":"Dominican Rep.", "厄瓜多尔":"Ecuador", "埃及" :"Egypt", "萨尔瓦多" :"ElSalvador", "赤道几内亚":"Eq.Guinea", "厄立特里亚":"Eritrea", "爱沙尼亚":"Estonia", "埃塞俄比亚":"Ethiopia", "福克兰群岛":"FalklandIs", "斐济" :"Fiji", "芬兰" :"Finland", "法国" :"France", "法属圭亚那":"FrenchGuiana", "法属南部领地":"Fr.S.AntarcticLands", "加蓬" :"Gabon", "冈比亚":"Gambia", "德国" :"Germany", "佐治亚州":"Georgia ", "加纳":"Ghana", "希腊":"Greece", "格陵兰岛":"Greenland", "危地马拉":"Guatemala", "几内亚":"Guinea", "几内亚比绍":"Guinea-Bissau", "圭亚那":"Guyana", "海地":"Haiti", "赫德岛和麦克唐纳群岛":"HeardI.andMcDonaldIs", "洪都拉斯":"Honduras", "匈牙利":"Hungary", "冰岛":"Iceland", "印度":"India", "印度尼西亚":"Indonesia", "伊朗":"Iran", "伊拉克":"Iraq", "爱尔兰":"Ireland", "以色列":"Israel", "意大利":"Italy", "象牙海岸":"IvoryCoast", "牙买加":"Jamaica", "日本" :"Japan", "乔丹":"Jordan", "克什米尔":"Kashmir", "哈萨克斯坦":"Kazakhstan", "肯尼亚":"Kenya", "科索沃":"Kosovo", "科威特":"Kuwait", "吉尔吉斯斯坦":"Kyrgyzstan", "老挝" :"Laos", "拉脱维亚":"Latvia", "黎巴嫩":"Lebanon", "莱索托":"Lesotho", "利比里亚":"Liberia", "利比亚":"Libya", "立陶宛":"Lithuania", "卢森堡":"Luxembourg", "马达加斯加":"Madagascar", "马其顿":"Macedonia", "马拉维":"Malawi", "马来西亚":"Malaysia", "马里":"Mali", "毛里塔尼亚":"Mauritania", "墨西哥":"Mexico", "摩尔多瓦":"Moldova", "蒙古国" :"Mongolia", "黑山" :"Montenegro", "摩洛哥":"Morocco", "莫桑比克":"Mozambique", "缅甸":"Myanmar", "纳米比亚":"Namibia", "荷兰" :"Netherlands", "新喀里多尼亚":"New Caledonia", "新西兰":"New Zealand", "尼泊尔":"Nepal", "尼加拉瓜":"Nicaragua", "尼日尔":"Niger", "尼日利亚":"Nigeria", "韩国":"Korea", "北塞浦路斯":"NorthernCyprus", "挪威":"Norway", "阿曼":"Oman", "巴基斯坦":"Pakistan", "巴拿马":"Panama", "巴布亚新几内亚":"Papua New Guinea", "巴拉圭":"Paraguay", "秘鲁":"Peru", "刚果":"Republi cofthe Congo", "菲律宾":"Philippines", "波兰":"Poland", "葡萄牙":"Portugal", "波多黎各":"Puerto Rico", "卡塔尔":"Qatar", "塞尔维亚共和国":"RepublicofSerbia", "罗马尼亚":"Romania", "俄罗斯":"Russia", "卢旺达":"Rwanda", "萨摩亚":"Samoa", "沙特阿拉伯":"Saudi Arabia", "塞内加尔":"Senegal", "塞尔维亚" :"Serbia", "塞拉利昂" :"Sierra Leone", "斯洛伐克":"Slovakia", "斯洛文尼亚":"Slovenia", "所罗门群岛":"SolomonIs", "索马里兰":"Somaliland", "索马里":"Somalia", "南非":"South Africa", "南乔治亚和南桑德威奇群岛":"S.Geo.andS.Sandw.Is", "南苏丹":"S. Sudan", "西班牙":"Spain", "斯里兰卡":"Sri Lanka", "苏丹" :"Sudan", "苏里南":"Suriname", "斯威士兰":"Swaziland", "瑞典" :"Sweden", "瑞士":"Switzerland", "叙利亚":"Syria", "塔吉克斯坦":"Tajikistan", "坦桑尼亚":"Tanzania", "泰国" :"Thailand", "东帝汶":"Timor-Leste", "多哥" :"Togo", "特立尼达和多巴哥":"TrinidadandTobago", "突尼斯":"Tunisia", "土耳其":"Turkey", "土库曼斯坦":"Turkmenistan", "乌干达":"Uganda", "乌克兰":"Ukraine", "大不列颠联合王国":"United Kingdom", "坦桑尼亚联合共和国":"UnitedRepublicofTanzania", "美国" :"United States", "美利坚合众国":"UnitedStatesofAmerica", "乌拉圭":"Uruguay", "乌兹别克斯坦":"Uzbekistan", "瓦努阿图":"Vanuatu", "委内瑞拉":"Venezuela", "越南":"Vietnam", "西岸" :"WestBank", "西撒哈拉":"W.Sahara", "也门":"Yemen", "赞比亚":"Zambia", "津巴布韦":"Zimbabwe" };

export default {
  name: "National",
  data () {
    return {
      case_data: {
        current_data: '',
        overall_data: '',
        cured_data: '',
        death_data: ''
      },
      activeName: 'first',
      activeName1: 'first',
      tableData: [],

    }
  },
  mounted () {
    this.getData ();
    this.mychart = echarts.init(this.$refs.mapbox);
    this.mychart.setOption(option);
    this.mychart1 = echarts.init(this.$refs.mapbox1);
    this.mychart1.setOption(option);
    this.getlineChart ();
    this.linechart = echarts.init(this.$refs.linechart);
    this.linechart.setOption(linec);
    this.linechart1 = echarts.init(this.$refs.linechart1);
    this.linechart1.setOption(linec1);
  },
  methods: {
    getData () {
      jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427', //接口
        {},
        (err,data)=>{
          if(!err){
            // get the summary data
            this.case_data.current_data = data.data.othertotal.ecertain;
            this.case_data.overall_data = data.data.othertotal.certain;
            this.case_data.cured_data = data.data.othertotal.recure;
            this.case_data.death_data = data.data.othertotal.die;

            // the map data
            let mapData = data.data.otherlist.map (item => ({name: nameMap[item.name], value: item.econNum}))
            let China = {name: 'China', value: data.data.econNum};
            mapData.push (China);
            option.series[0].data = mapData;
            this.mychart.setOption (option);

            let mapData1 = data.data.otherlist.map (item => ({name: nameMap[item.name], value: item.conNum}))
            let China1 = {name: 'China', value: data.data.gntotal};
            let Green = {name: 'Greenland', value: 13};
            mapData1.push (China1);
            mapData1.push (Green);
            option.series[0].data = mapData1;
            this.mychart1.setOption (option);

            // get table data
            this.tableData = data.data.otherlist;
            for (let i = 0; i < 149; i ++) {
              let cname = this.tableData[i].name;
              let ename = nameMap[cname];
              this.tableData[i].name = ename;
            }
          }
        })
    },
    getlineChart () {
      jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427',
          {},
          (err,data)=>{
            if(!err){
              let line_list = data.data.otherhistorylist.map (item => ({date: item.date, cer: item.certain}));
              let j = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec.xAxis[0].data[j] = line_list[i].date;

                j --;
              }
              let k = 148;
              for (let i = 0; i < 149; i = i + 1) {
                linec.series[0].data[k] = line_list[i].cer;
                k --;
              }
              this.linechart.setOption(linec);

              let line_list1 = data.data.otherhistorylist.map (item => ({date: item.date, cure: item.recure, death: item.die}));
              let jj = 148;
              // console.log (line_list1);
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
            }
          })
    },
  }
}
</script>