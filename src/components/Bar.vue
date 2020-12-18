<template>
  <div class="bar">
      <div id = "d1" class="horn">
        <div class="lt"></div>
        <div class="rt"></div>
        <div class="lb"></div>
        <div class="rb"></div>
      <img v-if="index>0" id="back1" src="../../static/back5.png" @click="goBack()">
      <div id="bar1"></div>
      <div id="supplier" @click="supplier()" style="color: white;cursor: pointer">供应商:{{text}}</div>
    </div>
    <div id="d2" class="horn">
      <div class="lt"></div>
      <div class="rt"></div>
      <div class="lb"></div>
      <div class="rb"></div>
      <div id="bar2"></div>
    </div>
    <div id="d3" class="horn">
      <div class="lt"></div>
      <div class="rt"></div>
      <div class="lb"></div>
      <div class="rb"></div>
    </div>
  </div>
</template>

<script>
    export default {
        name: "Bar",
        data() {
            return {
                objectData: {
                    data: []
                },
                myChart: '',
                myChart2:'',
                back1:'',
                proName:'',
                cityName:'',
                index:0,
                name:'',
                proId:1,
                city:1,
                position:[],
                sales:[],
                show:false,
                text:"显示",
                point:{
                    log:100.07,
                    lat:30.05,
                    height:4000000
                },
                cPoint:{
                    log:114.26735,
                    lat:30.60943,
                    height:2500000
                },
                proPoint:{
                    log:0.0,
                    lat:0.0,
                    height:0.0
                },
                cityPoint:{
                    log:0.0,
                    lat:0.0,
                    height:0.0
                }
            }
        },

        mounted() {
            this.myChart = this.$echarts.init(document.getElementById('bar1'));
            this.myChart2 = this.$echarts.init(document.getElementById('bar2'));
            this.drawPie(0);
            this.index--;
            this.myChart.on('click',  (param)=> {
                console.log(this.index);
                if(this.index===0){
                    this.name = param.name;
                    this.drawPie(1);
                }else if(this.index===1){
                    this.$axios.get('/static/province.json').then(res => {
                        for (var i = 0; i < res.data.length; i++) {
                            if(param.name===res.data[i].proname){
                                this.proName = param.name;
                                this.proId = parseInt(res.data[i].proid);
                                this.proPoint.log = res.data[i].point.split(",")[0];
                                this.proPoint.lat = res.data[i].point.split(",")[1];
                                this.proPoint.height = res.data[i].point.split(",")[2];
                                break;
                            }

                        }
                        this.drawPie(2,this.proId)
                    })

                }else if(this.index===2){
                    this.$axios.get('/static/city.json').then(res => {
                        for (var i = 0; i < res.data.length; i++) {
                            if(param.name===res.data[i].cityName){
                                this.cityName = param.name;
                                this.city = parseInt(res.data[i].cityID);
                                this.cityPoint.log = res.data[i].point.split(",")[0];
                                this.cityPoint.lat = res.data[i].point.split(",")[1];
                                this.cityPoint.height = res.data[i].point.split(",")[2];
                                break;
                            }

                        }
                        this.drawPie(3,this.proId,this.city)
                    })

                }

            });
        },

        methods: {
            goBack(){
                if(this.index>0){
                    this.index--;
                    this.drawPie(this.index,this.proId,this.city);
                    this.index--;
                }
            },

            supplier(){
                this.show=!this.show;
                if(this.show){
                    this.text="隐藏"
                }else {
                    this.text="显示"
                }
                this.$emit('chart-click',this.show);
            },

            drawBar() {
                this.myChart.setOption({
                    backgroundColor:'#003da8',//为了间隔设置底色
                    tooltip: {//提示框，可以在全局也可以在
                        trigger: 'item',  //提示框的样式
                        formatter: "{b}: {c} ({d}%)",
                        color: '#000', //提示框的背景色
                        textStyle: { //提示的字体样式
                            color: "white",
                        }
                    },
                    title:{
                        text:(this.index>0?this.name:'')+(this.index>1?this.proName:'全国')+(this.index>2&&this.proName!==this.cityName?this.cityName:'')+'销量分布图',
                        left:'center'
                    },
                    series: [
                        {
                            name: '访问来源',
                            type: 'pie', //环形图的type和饼图相同
                            center:['50%', '50%'],
                            radius: ['50%', '60%'],//饼图的半径，第一个为内半径，第二个为外半径
                            // hoverAnimation:false,//取消鼠标点击动画
                            // color: ['#D1FBEF', '#F9D858', '#4CD0DD', '#DF86F0', '#F5A7C1'],
                            label: {
                                // formatter: '{a|{b}\n{d}%}',
                                formatter(v) {
                                    let text = v.name+Math.round(v.percent)+'%'
                                    if(text.length <= 6)
                                    {
                                        return text;
                                    }else if(text.length > 6 && text.length <= 12){
                                        return text = `${text.slice(0,6)}\n${text.slice(6)}`
                                    }else if(text.length > 12 && text.length <= 18){
                                        return text = `${text.slice(0,6)}\n${text.slice(6,12)}\n${text.slice(12)}`
                                    }else if(text.length > 18 && text.length <= 24){
                                        return text = `${text.slice(0,6)}\n${text.slice(6,12)}\n${text.slice(12,18)}\n${text.slice(18)}`
                                    }else if(text.length > 24){
                                        return text = `${text.slice(0,6)}\n${text.slice(6,12)}\n${text.slice(12,18)}\n${text.slice(18,24)}\n${text.slice(24)}`
                                    }
                                },
                                align:'left',
                                borderRadius: 4,
                                color: '#D1FBEF',
                                fontFamily: 'PingFangSC-Regular',
                                fontSize: '12px',
                                rich: {
                                    a: {
                                        color: '#D1FBEF',
                                        lineHeight: 17,
                                        align: 'center'
                                    }
                                }
                            },
                          //提示文字，折线的大小
                          //设置圆环之间的间隔，只要颜色一致即可
                          itemStyle:{
                            borderWidth: 5,//设置间隔
                            borderColor:'#003da8',
                          },
                            data: this.objectData.data
                        }
                    ]
                });
                //提取数据列表中的地区名
                var myChart2BarName = this.objectData.data;
                var BarName = [];
                var sum = 0;
                function getName(){
                    for(var i = 0;i < myChart2BarName.length ; i++ ){
                       BarName[i] = myChart2BarName[i].name;
                    }
                    return BarName;
                }
                //获取总销售额
                function getAllValue(){
                  for(var i = 0;i < myChart2BarName.length ; i++ ){
                    sum += myChart2BarName[i].value;
                  }
                  return sum;
                }
                //实现水平条形图显示
                this.myChart2.setOption({
                    tooltip: {//提示框，可以在全局也可以在
                        trigger: 'axis',  //提示框的样式
                        formatter: "{b}:{c}",
                        color: '#000', //提示框的背景色
                        textStyle: { //提示的字体样式
                          color: "white",
                        },
                        axisPointer:{
                          type:'none'
                        }
                    },
                //调整Echart的位置
                    grid:{
                      left:'5%',
                      right:'5%',
                      bottom:'5%',
                      top:'10%',
                      containLabel: true,
                    },
                    backgroundColor:'rgba(0,0,255,0.5)',
                    title:[{
                        text:(this.index>0?this.name:'')+(this.index>1?this.proName:'全国')+(this.index>2&&this.proName!==this.cityName?this.cityName:'')+'销量条形图',
                        left:'center'
                    },
                      {
                        text:'销售总额：'+ getAllValue() +'辆',
                        left:'right',
                        bottom:'2%',
                        right:'5%',
                        textStyle:{
                          color:'blue',//文字颜色
                          fontSize:20,
                        }
                      }],
                    xAxis:{
                        show:false,//不显示横轴
                        type:'value',
                    },
                    yAxis:[{
                      //第一个y轴
                        type:'category',
                        inverse:true,
                        //去掉y轴
                        axisLine:{
                          show:false,
                        },
                        //去掉刻度线
                        axisTick: {
                          show: false
                        },
                        splitLine: {
                          show: false
                        },
                        axisLabel:{
                            textStyle:{
                            color:'#ffffff',
                            fontSize:'12'
                          },
                        },
                        data: getName()
                      },
                  //第二个y轴
                  {
                    type:'category',
                    inverse: true,
                    axisLine: 'none',
                    axisTick: 'none',
                    show:true,
                    axisLabel:{
                     textStyle:{
                       color:'#ffffff',
                       fontSize:'12'
                     },
                    },
                    data: this.objectData.data
                  }
                ],
                series:[{
                  name:'销量',
                  type: 'bar',
                  stack: 'chart',
                  silent: true,
                  label: {
                    // formatter: '{a|{b}\n{d}%}',
                    //文字换行
                    formatter(v) {
                      let text = v.name + Math.round(v.percent) + '%'
                      if (text.length <= 6) {
                        return text;
                      } else if (text.length > 6 && text.length <= 12) {
                        return text = `${text.slice(0, 6)}\n${text.slice(6)}`
                      } else if (text.length > 12 && text.length <= 18) {
                        return text = `${text.slice(0, 6)}\n${text.slice(6, 12)}\n${text.slice(12)}`
                      } else if (text.length > 18 && text.length <= 24) {
                        return text = `${text.slice(0, 6)}\n${text.slice(6, 12)}\n${text.slice(12, 18)}\n${text.slice(18)}`
                      } else if (text.length > 24) {
                        return text = `${text.slice(0, 6)}\n${text.slice(6, 12)}\n${text.slice(12, 18)}\n${text.slice(18, 24)}\n${text.slice(24)}`
                      }
                    },
                    // color: '#D1FBEF',
                    position:'right',
                    fontFamily: 'PingFangSC-Regular',
                    fontSize: '12px',
                  },
                  itemStyle:{
                    normal:{
                      // 不同柱子不同颜色且颜色渐变
                      color:function(params){
                        var colorList = [
                          ['#0679e3', '#90c1fc'],
                          ['#07b8d9', '#86e9fc'],
                          ["#E56E6E", "#EFA4A4"],
                          ["#FEB763", "#F9CF9E"],
                          ["#00C0DD", "#00C0DD"],
                          ["#23C83E", "#9BF194"],
                          ["#1AA291", "#1AA291"],
                          ["#4186EC", "#3AB3FB"],
                          ['#3d97ed', '#62c4db'],
                          ['#0679e3', '#90c1fc'],
                          ['#07b8d9', '#86e9fc'],
                          ["#E56E6E", "#EFA4A4"],
                          ["#FEB763", "#F9CF9E"],
                          ["#00C0DD", "#00C0DD"],
                          ["#23C83E", "#9BF194"],
                          ["#1AA291", "#1AA291"],
                          ["#4186EC", "#3AB3FB"],
                          ['#3d97ed', '#62c4db'],
                          ['#0679e3', '#90c1fc'],
                          ['#07b8d9', '#86e9fc'],
                          ["#E56E6E", "#EFA4A4"],
                          ["#FEB763", "#F9CF9E"],
                          ["#00C0DD", "#00C0DD"],
                          ["#23C83E", "#9BF194"],
                          ["#1AA291", "#1AA291"],
                          ["#4186EC", "#3AB3FB"],
                          ['#3d97ed', '#62c4db'],
                        ];
                        var _this = this;
                        var index = params.dataIndex;
                        return {
                          colorStops:[
                              {
                                offset:0,//颜色开始的位置
                                color:colorList[index][0]},//0%处的颜色
                              {
                                offset: 1,//颜色结束的位置
                                color:colorList[index][1]//100%处的颜色
                              }
                              ]
                        }
                      },
                      barBorderRadius: 30,
                    }
                  },
                  barWidth:8,//柱状图宽度
                  //调整间距
                  // barCategoryGap: '50%',
                  // barGap:'80%',
                  data: this.objectData.data
                },
                ]
              });
            },

            getData(index,url,max,min){
                this.$axios.get(url).then(res => {
                    this.objectData.data=[];
                    this.position=[];
                    for (var i = 0; i < res.data.length; i++) {
                        var map = {
                            "name": "",
                            "value": 1
                        };
                        map.name = res.data[i].proname;
                        map.value = parseInt(Math.random()*(max-min+1)+min,10);
                        this.objectData.data[i] = map;

                        if(index>0){
                            var point = {
                                "log": 0.0,
                                "lat": 0.0
                            };
                            point.log = res.data[i].point.split(",")[0];
                            point.lat = res.data[i].point.split(",")[1];
                            this.position.push(point);
                        }
                    }
                    this.$emit('bar-click',this.position,this.objectData,this.cPoint,this.index);
                    this.drawBar()
                })

            },

            drawPie(index,proId,city){
                if(index===0){
                    this.$axios.get('/static/car.json').then(res => {
                        this.objectData.data=[];
                        for (var i = 0; i < res.data.length; i++) {
                            var map = {
                                "name": "",
                                "value": 1
                            };
                            map.name = res.data[i].type;
                            map.value = res.data[i].sale;
                            this.objectData.data[i] = map;
                        }
                        this.$emit('bar-click',this.position,this.objectData,this.point,this.index);
                        this.drawBar()
                    })
                }else if(index===1){
                    this.$axios.get('/static/province.json').then(res => {
                        this.objectData.data=[];
                        this.position=[];
                        for (var i = 0; i < res.data.length; i++) {
                            var map = {
                                "name": "",
                                "value": 1
                            };
                            map.name = res.data[i].proname;
                            map.value = parseInt(Math.random()*(2000-1000+1)+1000,10);
                            this.objectData.data[i] = map;
                            var point = {
                                "log": 0.0,
                                "lat": 0.0
                            };
                            point.log = res.data[i].point.split(",")[0];
                            point.lat = res.data[i].point.split(",")[1];
                            this.position.push(point);
                        }
                        this.$emit('bar-click',this.position,this.objectData,this.cPoint,this.index);
                        this.drawBar()
                    })
                }else if(index===2){
                    this.$axios.get('/static/city.json').then(res => {
                        this.objectData.data=[];
                        this.position=[];
                        for (var i = 0; i < res.data.length; i++) {
                            if(proId===parseInt(res.data[i].proID)){
                                var map = {
                                    "name": "",
                                    "value": 1
                                };
                                map.name = res.data[i].cityName;
                                map.value = parseInt(Math.random()*(200-100+1)+100,10);
                                this.objectData.data.push(map);
                                var point = {
                                    "log": 0.0,
                                    "lat": 0.0
                                };
                                point.log = res.data[i].point.split(",")[0];
                                point.lat = res.data[i].point.split(",")[1];
                                this.position.push(point);
                            }
                        }
                        this.$emit('bar-click',this.position,this.objectData,this.proPoint,this.index);
                        this.drawBar()
                    })
                }else if(index===3){
                    var url;
                    if(this.name==="宝骏E100"){
                        url='/static/baojun1.json'
                    }else if(this.name==="宝骏E200"){
                        url='/static/baojun2.json'
                    }else {
                        url='/static/hongguang.json'
                    }
                    this.$axios.get(url).then(res => {
                        this.objectData.data=[];
                        this.position=[];
                        var prev = false;
                        var curr = false;
                        for (var i = 0; i < res.data.length; i++) {
                            if(city===parseInt(res.data[i].city)){
                                prev=curr;
                                curr= true;
                                var map = {
                                    "name": "",
                                    "value": 1
                                };
                                map.name = res.data[i].company;
                                map.value = parseInt(Math.random()*(20-4+1)+4,10);
                                this.objectData.data.push(map);
                                var point = {
                                    "log": 0.0,
                                    "lat": 0.0
                                };
                                point.log = res.data[i].point.split(",")[0];
                                point.lat = res.data[i].point.split(",")[1];
                                this.position.push(point);
                            }else {
                                prev = curr;
                                curr = false;
                            }
                            if(prev&&!curr){
                                break;
                            }

                        }
                        this.$emit('bar-click',this.position,this.objectData,this.cityPoint,this.index);
                        this.drawBar()
                    })
                }
                this.index++;
            }

        }

    }
</script>

<style scoped>
  .bar {
    position: relative;

  }

  .bar #d1 {
    position: absolute;
    width: 98%;
    height: 30%;
    top: 10%;
    background: #003da8;
  }

  .bar #back1{
    position: absolute;
    left:5%;
    top: 2%;
  }

  .bar #bar1{
    position: absolute;
    width: 100%;
    /*top:9%;*/
    height: 100%;
    background: #003da8;
  }

  .bar #supplier{
    position: absolute;
    width: 20%;
    height: 10%;
    bottom:1%;
    right:1%;
  }

  .bar #d2 {
    position: absolute;
    width: 98%;
    top: 41%;
    height: 30%;
    background: #22ee22;
  }

  .bar #bar2{
    position: absolute;
    width: 100%;
    height: 100%;
  }
  /*添加边框样式*/
  .bar #d3 {
    position: absolute;
    width: 98%;
    top: 72%;
    height: 30%;
    background: rgba(11,137,250,0.7);
  }

  .horn{
    position: absolute;
    border: 1px solid #00d3e7;
    bottom: 280px;
  }
  .horn>div{
    width: 10px;
    height: 10px;
    position: absolute;
  }
  .horn .lt{
    border-top: 2px solid #00d3e7;
    border-left: 2px solid #00d3e7;
    left: 0px;
    top: 0px;
  }
  .horn .rt{
    border-top: 2px solid #00d3e7;
    border-right: 2px solid #00d3e7;
    right: 0px;
    top: 0px;
  }
  .horn .lb{
    border-bottom: 2px solid #00d3e7;
    border-left: 2px solid #00d3e7;
    left: 0px;
    bottom: 0px;
  }
  .horn .rb{
    border-bottom: 2px solid #00d3e7;
    border-right: 2px solid #00d3e7;
    right: 0px;
    bottom: 0px;
  }

</style>
