<template>
  <div class="main">
    <bar id="bar"></bar>
    <div id="cesiumContainer"></div>
  </div>

</template>

<script>
    import '../../node_modules/cesium/Build/Cesium/Widgets/Widgets.css'
    import bar from './Bar'
    export default {
        name: "Main",
        components:{
            bar
        },
        data(){
            return{
                viewer:'',
            }
        },
        mounted() {
            this.init()
        },
        methods: {
            init() {
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
                });

                this.viewer.scene.imageryLayers.addImageryProvider(
                    new Cesium.UrlTemplateImageryProvider({
                        url: 'world4/world4/{z}/{x}/{y}.jpg',
                        fileExtension: 'jpg'
                    })
                );

                this.viewer.camera.setView({
                    destination:Cesium.Cartesian3.fromDegrees(100.07,30.05,4000000),
                    orientation:{
                        heading:Cesium.Math.toRadians(0),
                        pitch:Cesium.Math.toRadians(-90),
                        roll:Cesium.Math.toRadians(0),
                    }
                });

                this.viewer._cesiumWidget._creditContainer.style.display = 'none'
            }
        }
    }
</script>

<style scoped>
  .main{
    height: calc(100vh - 20px);
    position:relative
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
