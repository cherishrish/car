<template>
  <div class="main">
    <bar id="bar" @bar-click="onBarClick"></bar>
    <div id="cesiumContainer"></div>
  </div>

</template>

<script>
    import '../../node_modules/cesium/Build/Cesium/Widgets/Widgets.css'
    import bar from './Bar'

    export default {
        name: "Main",
        components: {
            bar
        },
        data() {
            return {
                viewer: '',
                tempEntities:[]
            }
        },
        mounted() {
            this.init()
        },
        methods: {
            init() {
                //在线天地图影像服务地址(经纬度)
                var TDT_IMG_C="http://{s}.tianditu.gov.cn/img_c/wmts?service=wmts&request=GetTile&version=1.0.0" +
                    "&LAYER=img&tileMatrixSet=c&TileMatrix={TileMatrix}&TileRow={TileRow}&TileCol={TileCol}" +
                    "&style=default&format=tiles&tk=04a1ed562408cfab1fb4d6f6d3d5b77d";

                this.viewer = new Cesium.Viewer('cesiumContainer', {
                    geocoder: false, // 地理位置查询定位控件
                    homeButton: false, // 默认相机位置控件
                    timeline: false, // 时间滚动条控件
                    navigationHelpButton: false, // 默认的相机控制提示控件
                    fullscreenButton: false, // 全屏控件
                    scene3DOnly: true, // 每个几何实例仅以3D渲染以节省GPU内存
                    baseLayerPicker: false, // 底图切换控件
                    animation: false, // 控制场景动画的播放速度控件
                    infoBox: false,
                    selectionIndicator: false,
                    //天地图影像服务（经纬度）
                    imageryProvider:new Cesium.WebMapTileServiceImageryProvider({
                        url: TDT_IMG_C,
                        layer: "tdtImg_c",
                        style: "default",
                        format: "tiles",
                        tileMatrixSetID: "c",
                        subdomains:["t0","t1","t2","t3","t4","t5","t6","t7"],
                        tilingScheme:new Cesium.GeographicTilingScheme(),
                        tileMatrixLabels:["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19"],
                        maximumLevel:50,
                        show: false
                    })
                });

                //在线天地图影像中文标记服务(经纬度)
                var TDT_CIA_C = "http://{s}.tianditu.gov.cn/cia_c/wmts?service=wmts&request=GetTile&version=1.0.0" +
                    "&LAYER=cia&tileMatrixSet=c&TileMatrix={TileMatrix}&TileRow={TileRow}&TileCol={TileCol}" +
                    "&style=default&format=tiles&tk=04a1ed562408cfab1fb4d6f6d3d5b77d";

                // this.viewer.scene.imageryLayers.addImageryProvider(
                //     new Cesium.UrlTemplateImageryProvider({
                //         url: 'world4/world4/{z}/{x}/{y}.jpg',
                //         fileExtension: 'jpg'
                //     })
                // );

                this.viewer.imageryLayers.addImageryProvider(new Cesium.WebMapTileServiceImageryProvider({   //调用影响中文注记服务
                    url: TDT_CIA_C,
                    layer: "tdtImg_c",
                    style: "default",
                    format: "tiles",
                    tileMatrixSetID: "c",
                    subdomains: ["t0", "t1", "t2", "t3", "t4", "t5", "t6", "t7"],
                    tilingScheme: new Cesium.GeographicTilingScheme(),
                    tileMatrixLabels: ["1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12", "13", "14", "15", "16", "17", "18", "19"],
                    maximumLevel: 50,
                    show: false
                }));


                // this.viewer.camera.setView({
                //     destination:Cesium.Cartesian3.fromDegrees(100.07,30.05,4000000),
                //     orientation:{
                //         heading:Cesium.Math.toRadians(0),
                //         pitch:Cesium.Math.toRadians(-90),
                //         roll:Cesium.Math.toRadians(0),
                //     }
                // });

                this.viewer._cesiumWidget._creditContainer.style.display = 'none'
            },

            onBarClick(position, data, point, index) {
                console.log('index:'+index);
                console.log('main' + point.height);
                if (index === 0) {
                    this.clearEntities();
                    this.flyTo(point)
                } else if (index === 1) {
                    this.flyTo(point);
                    this.drawEntities(position, data)
                } else if (index === 2) {
                    this.flyTo(point);
                    this.drawEntities(position, data)
                } else if (index === 3) {
                    this.flyTo(point);
                    this.drawEntities(position, data)
                }
            },

            flyTo(point) {
                this.viewer.camera.flyTo({
                    destination: Cesium.Cartesian3.fromDegrees(point.log, point.lat, point.height),
                    orientation: {
                        heading: Cesium.Math.toRadians(0),
                        pitch: Cesium.Math.toRadians(-90),
                        roll: Cesium.Math.toRadians(0),
                    }
                });
            },

            drawEntities(position, data) {
               this.clearEntities();
                for (var i = 0; i < position.length; i++) {
                    var entity = this.viewer.entities.add({
                        position: new Cesium.Cartesian3.fromDegrees(position[i].log, position[i].lat),
                        billboard: {
                            image: '../../static/marker.png',
                            scale: 1,
                            horizontalOrigin: Cesium.HorizontalOrigin.CENTER,
                            verticalOrigin: Cesium.VerticalOrigin.BOTTOM,
                            heightReference: Cesium.HeightReference.CLAMP_TO_GROUND
                        },
                        label: {
                            text: data.data[i].name + ':' + data.data[i].value + '辆',
                            font: '15px Helvetica',
                            fillColor: Cesium.Color.BLUE,
                            pixelOffset: new Cesium.Cartesian2(0, 10),
                        }
                    });
                    this.tempEntities.push(entity);
                }
            },

            clearEntities(){
                if(this.tempEntities.length>0){
                    for (var i = 0; i <  this.tempEntities.length; i++) {
                        this.viewer.entities.remove(this.tempEntities[i]);
                    }
                    this.tempEntities=[];
                }
            }
        }
    }
</script>

<style scoped>
  .main {
    height: calc(100vh - 20px);
    position: relative
  }

  .main #bar {
    position: absolute;
    width: 30%;
    height: 100%;
  }

  .main #cesiumContainer {
    position: absolute;
    left: 30%;
    width: 70%;
    height: 100%;
  }

</style>
