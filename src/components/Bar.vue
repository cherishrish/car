<template>
  <div class="bar">
    <div id="bar1"></div>
    <div id="bar2"></div>
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
                index:0,
                name:'',
                proId:1,
                city:1
            }
        },

        mounted() {
            this.myChart = this.$echarts.init(document.getElementById('bar1'));
            this.init()
            this.myChart.on('click',  (param)=> {
                console.log(this.index)
                if(this.index===0){
                    this.name = param.name;
                    this.drawPie(1);
                }else if(this.index===1){
                    this.$axios.get('/static/province.json').then(res => {
                        for (var i = 0; i < res.data.length; i++) {
                            if(param.name===res.data[i].proname){
                                this.proId = parseInt(res.data[i].proid);
                                break;
                            }

                        }
                        this.drawPie(2,this.proId)
                    })

                }else if(this.index===2){
                    this.$axios.get('/static/city.json').then(res => {
                        for (var i = 0; i < res.data.length; i++) {
                            if(param.name===res.data[i].cityName){
                                this.city = parseInt(res.data[i].cityID);
                                break;
                            }

                        }
                        this.drawPie(3,this.proId,this.city)
                    })

                }

            });
        },

        methods: {
            init() {
                this.$axios.get('/static/car.json').then(res => {
                    for (var i = 0; i < res.data.length; i++) {
                        var map = {
                            "name": "",
                            "value": 1
                        };
                        map.name = res.data[i].type;
                        map.value = res.data[i].sale;
                        this.objectData.data[i] = map;
                    }
                    console.log(this.objectData.data);
                    this.drawBar()
                })
            },

            drawBar() {
                this.myChart.setOption({
                    tooltip: {//提示框，可以在全局也可以在
                        trigger: 'item',  //提示框的样式
                        formatter: "{b}: {c} ({d}%)",
                        color: '#000', //提示框的背景色
                        textStyle: { //提示的字体样式
                            color: "black",
                        }
                    },
                    series: [
                        {
                            name: '访问来源',
                            type: 'pie', //环形图的type和饼图相同
                            radius: ['40%', '60%'],//饼图的半径，第一个为内半径，第二个为外半径
                            // color: ['#D1FBEF', '#F9D858', '#4CD0DD', '#DF86F0', '#F5A7C1'],
                            label: {
                                formatter: '{a|{b}\n{d}%}',
                                borderWidth: 1,
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
                            },  //提示文字
                            data: this.objectData.data
                        }
                    ]
                });

            },

            drawPie(index,proId,city){
                if(index===1){
                    this.$axios.get('/static/province.json').then(res => {
                        this.objectData.data=[];
                        for (var i = 0; i < res.data.length; i++) {
                            var map = {
                                "name": "",
                                "value": 1
                            };
                            map.name = res.data[i].proname;
                            map.value = parseInt(Math.random()*(2000-1000+1)+1000,10);
                            this.objectData.data[i] = map;
                        }
                        this.drawBar()
                    })
                }else if(index===2){
                    this.$axios.get('/static/city.json').then(res => {
                        this.objectData.data=[];
                        for (var i = 0; i < res.data.length; i++) {
                            if(proId===parseInt(res.data[i].proID)){
                                var map = {
                                    "name": "",
                                    "value": 1
                                };
                                map.name = res.data[i].cityName;
                                map.value = parseInt(Math.random()*(200-100+1)+1000,10);
                                this.objectData.data.push(map)
                            }
                        }
                        console.log(this.objectData.data);
                        this.drawBar()
                    })
                }else if(index===3){
                    if(this.name==="宝骏E100"){
                        this.$axios.get('/static/baojun1.json').then(res => {
                            this.objectData.data=[];
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
                                    map.value = parseInt(Math.random()*(20-4+1)+1000,10);
                                    this.objectData.data.push(map)
                                }else {
                                    prev = curr;
                                    curr = false;
                                }
                                if(prev&&!curr){
                                    break;
                                }

                            }
                            console.log(this.objectData.data);
                            this.drawBar()
                        })
                    }
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

  .bar #bar1 {
    position: absolute;
    width: 100%;
    height: 50%;
    background: #003da8;
  }

  .bar #bar2 {
    position: absolute;
    width: 100%;
    top: 50%;
    height: 50%;
    background: #22ee22;
  }

</style>
