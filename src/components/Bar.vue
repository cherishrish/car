<template>
  <div class="bar">
<!--    加上边框样式-->
    <div id = "d1" class="horn">
      <div class="lt"></div>
      <div class="rt"></div>
      <div class="lb"></div>
      <div class="rb"></div>
      <img v-if="index>0" id="back1" src="../../static/back.png" @click="goBack()">
      <div id="bar1"></div>
    </div>
    <div id="d2" class="horn">
      <div class="lt"></div>
      <div class="rt"></div>
      <div class="lb"></div>
      <div class="rb"></div>
      <div id="bar2"></div>
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

            drawBar() {
                this.myChart.setOption({
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
                            radius: ['40%', '60%'],//饼图的半径，第一个为内半径，第二个为外半径
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
                                borderWidth: 1,
                                borderRadius: 4,
                                // color: '#D1FBEF',
                                fontFamily: 'PingFangSC-Regular',
                                fontSize: '12px',
                                rich: {
                                    a: {
                                        color: '#D1FBEF',
                                        lineHeight: 17,
                                        align: 'center'
                                    }
                                }
                            },  //提示文字
                            data: this.objectData.data
                        }
                    ]
                });
              //实现水平柱状图显示
              this.myChart2.setOption({
                tooltip: {//提示框，可以在全局也可以在
                  trigger: 'axis',  //提示框的样式
                  formatter: "{b}:{c}",
                  color: '#000', //提示框的背景色
                  textStyle: { //提示的字体样式
                    color: "black",
                  },
                  axisPointer:{
                    type:'none'
                  }
                },
                // legend:{
                //   top:"10%",
                //   data:['2019','2020','2021']
                // },
                grid:{
                  left:100
                },
                title:{
                  text:(this.index>0?this.name:'')+(this.index>1?this.proName:'全国')+(this.index>2&&this.proName!==this.cityName?this.cityName:'')+'销量分布图',
                  left:'center'
                },
                xAxis:{
                  show:false,//不显示横轴
                  type:'value',
                },
                yAxis:{
                  name:'车型',
                  type:'category',
                  inverse:true,
                  // data:['宏光','宝骏E100','宝骏E200'],
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
                  //在柱状图上显示销量
                  axisLabel:{
                    rich:{
                      value:{
                        lineHeight:10,
                        align:'center'
                      }
                    }
                  }
                },
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
                  // itemStyle:{
                  //   normal:{
                  //     color:'#f3f3f6'
                  //   }
                  // },
                  barWidth:5,//柱状图宽度
                  // barGap:'-100%',
                  data: this.objectData.data
                },
                  // {
                  //   name:'2020',
                  //   type:'bar',
                  //   data:['200','500','700'],
                  //   barWidth:10,//柱状图宽度
                  // },
                  // {
                  //   name:'2021',
                  //   type:'bar',
                  //   data:['900','1000','800'],
                  //   barWidth:10,//柱状图宽度
                  // },
                  ]
              });

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
                console.log(index);
                this.index++;
            }

        }

    }
</script>

<style scoped>
  .bar {
    position: relative;
    background: rgba(11,137,250,0.7);
  }

  .bar #d1 {
    position: absolute;
    width: 98%;
    height: 30%;
    /*background: #003da8;*/
    top:10%;
  }

  .bar #back1{
    position: absolute;
    right:5%;
  }

 .bar #bar1{
    position: absolute;
    width: 100%;
    /*top:9%;*/
    height: 100%;
    /*background: #003da8;*/
  }

  .bar #d2 {
    position: absolute;
    width: 98%;
    top: 41%;
    height: 30%;
    /*background: rgba(0,0,255,0.1);*/
  }

  .bar #bar2{
    position: absolute;
    width: 100%;
    height: 100%;
    /*background: rgba(0,0,255,0.1);*/
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
