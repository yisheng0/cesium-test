<template>
  <div id="cesiumContainer"></div>
</template>
<script setup>
import * as Cesium from "cesium";
import { onMounted } from "vue";
// Cesium.CESIUM_BASE_URL = '/'
//设置Token
Cesium.Ion.defaultAccessToken =
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJiNWE4YTIxMi1kNjFkLTRlYTUtOWQ5ZS03ZGY1ZmYzNzhiMWYiLCJpZCI6MTMwOTM2LCJpYXQiOjE2Nzk5ODg4MDB9.pt9e_0ZLH-i_72fM-WBrvWKYn0lDpDCzCFUdfAj1szQ";

////天地图token
let TDT_tk = "token";
//Cesium token
let cesium_tk = "cesium token";
//天地图影像
let TDT_IMG_C =
  "http://{s}.tianditu.gov.cn/img_c/wmts?service=wmts&request=GetTile&version=1.0.0" +
  "&LAYER=img&tileMatrixSet=c&TileMatrix={TileMatrix}&TileRow={TileRow}&TileCol={TileCol}" +
  "&style=default&format=tiles&tk=	cddbcc0145ad7a9783daa5a6110ec23f";

//设置cesium默认视角

Cesium.Camera.DEFAULT_VIEW_RECTANGLE = Cesium.Rectangle.fromDegrees(
  //西 南 东 北
  89.5,
  20.4,
  110.4,
  61.2
);
onMounted(() => {
  const viewer = new Cesium.Viewer("cesiumContainer", {
    //地形
    terrainProvider: Cesium.createWorldTerrain({
      requestVertexNormals: true,
      requestWaterMask: true,
    }),

    //删除提示框
    infoBox: false,
    //搜索框
    // geocoder: false,
    //home
    homeButton: false,
    //查看器的显示模式
    sceneModePicker: false,
    //图层的选择
    baseLayerPicker: false,
    //帮助按钮
    navigationHelpButton: false,
    // 底部时间
    timeline: false,
    //全屏按钮
    fullscreenButton: false,
    //天空盒子
    // skyBox: new Cesium.SkyBox({
    //   sources: {
    //     positiveX: "./Wieght/SkyBox/36930ecdacac95f9c91256722ddadf22.jpg",
    //     negativeX: "./assets/36930ecdacac95f9c91256722ddadf22.jpg",
    //     positiveY: "./assets/36930ecdacac95f9c91256722ddadf22.jpg",
    //     negativeY: "./assets/36930ecdacac95f9c91256722ddadf22.jpg",
    //     positiveZ: "./assets/36930ecdacac95f9c91256722ddadf22.jpg",
    //     negativeZ: "./assets/36930ecdacac95f9c91256722ddadf22.jpg",
    //   },
    // }),
    //天地图服务
    imageryProvider: new Cesium.WebMapTileServiceImageryProvider({
      url: TDT_IMG_C,
      layer: "tdtImg_c",
      style: "default",
      format: "tiles",
      tileMatrixSetID: "c",
      subdomains: ["t0", "t1", "t2", "t3", "t4", "t5", "t6", "t7"],
      tilingScheme: new Cesium.GeographicTilingScheme(),
      tileMatrixLabels: [
        "1",
        "2",
        "3",
        "4",
        "5",
        "6",
        "7",
        "8",
        "9",
        "10",
        "11",
        "12",
        "13",
        "14",
        "15",
        "16",
        "17",
        "18",
        "19",
      ],
      maximumLevel: 50,
      show: false,
    }),
  });
  //隐藏logo
  viewer.cesiumWidget.creditContainer.style.display = "none";
  //1加载geojson数据
  // let dataGeoJson = Cesium.GeoJsonDataSource.load(
  //   "https://geo.datav.aliyun.com/areas_v3/bound/100000_full.json",
  //   {
  //     //stroke 边框
  //     stroke: Cesium.Color.RED,
  //     fill: Cesium.Color.SKYBLUE.withAlpha(0.8),
  //     strokeWidth: 3,
  //   }
  // );
  // dataGeoJson.then((data) => {
  //   viewer.dataSources.add(data);
  //   let entities = data.entities.values;
  //   entities.forEach((e, i) => {
  //     e.polygon.material = new Cesium.ColorMaterialProperty(
  //       Cesium.Color.fromRandom({
  //         alpha: 0.9,
  //       })
  //     );

  //   });
  // });
  //2  KML数据
  //3 CZML数据
  //cartographicDegrees：[时间 经度 纬度 高度]
  //相机
  //天安门
  let position = Cesium.Cartesian3.fromDegrees(116.397428, 39.90923, 1000);
  //广州塔
  let position2 = Cesium.Cartesian3.fromDegrees(113.3191, 23.109, 2000);
  ///瞬间到达指定位置
  // viewer.camera.setView({
  //   // destination: Cesium.Cartesian3.fromDegrees(position),
  //   destination: position,
  //   orientation: {
  //     //朝向
  //     heading: Cesium.Math.toRadians(0),
  //     //俯仰角
  //     pitch: Cesium.Math.toRadians(-60),
  //     //翻滚角
  //     roll: 0,
  //   },
  // });
  //fly to,让相机飞到某个地方
  viewer.camera.flyTo({
    destination: position2,
    orientation: {
      //朝向
      heading: Cesium.Math.toRadians(0),
      //俯仰角
      pitch: Cesium.Math.toRadians(-60),
      //翻滚角
      roll: 0,
    },
  });
  //创建一个点
  let point = viewer.entities.add({
    //定位点
    position: position2,
    // 点
    point: {
      pixelSize: 10,
      color: Cesium.Color.RED,
      outlineColor: Cesium.Color.WHEAT,
      outlineWidth: 4,
    },
  });
  //添加3D建筑
  const omsBuilding = viewer.scene.primitives.add(
    // new Cesium.Cesium3DTileset({
    //   url: ''
    //  })
    new Cesium.createOsmBuildings()
  );
  //添加文字标签 广告牌
  let label = viewer.entities.add({
    position: position2,
    label: {
      text: "广州塔",
      font: "24px",
      fillColor: Cesium.Color.WHEAT,
      outlineColor: Cesium.Color.BLUE,
      outlineWidth: 4,
      horizontalOrigin: Cesium.HorizontalOrigin.CENTER,
      verticalOrigin: Cesium.VerticalOrigin.TOP,
    },
  });
  //添加3D模型
  const airplane = viewer.entities.add({
    name: "Airplane",
    position: position2,
    model: {
      uri: "../public/model/bell_boeing_v22_osprey.glb",
      //最小像素
      minimumPixelSize: 128,
      //设置轮廓
      silhouetteSize: 1,
    },
  });
  //通过按键移动
  document.addEventListener("keydown", (event) => {
    if (event.key == "w") {
      viewer.camera.moveForward(10);
    }
    if (event.key == "s") {
      viewer.camera.moveBackward(10);
    }
    if (event.key == "a") {
      viewer.camera.moveLeft(10);
    }
    if (event.key == "d") {
      viewer.camera.moveRight(10);
    }
    // if (event.key == "e") {
    //   viewer.camera.lookRight(10);
    // }
    if (event.key == "e") {
      viewer.camera.lookRight(Cesium.Math.toRadians(10));
    }
  });

  //1创建图形几何
  let Rectangle = new Cesium.RectangleGeometry({
    rectangle: Cesium.Rectangle.fromDegrees(115, 20, 135, 30),
    height: 0,
    vertexFormat: Cesium.PerInstanceColorAppearance.VERTEX_FORMAT,
  });
  //2创建图形实例对象
  let instance = new Cesium.GeometryInstance({
    geometry: Rectangle,
    attributes: {
      color: Cesium.ColorGeometryInstanceAttribute.fromColor(
        Cesium.Color.BLUE.withAlpha(0.5)
      ),
    },
  });
  //3设置外观
  // let appearance = new Cesium.PerInstanceColorAppearance({
  //   flat: true,
  // });
  //1
  // const material = Cesium.Material.fromType("Color", {
  //   color: new Cesium.Color(1.0, 0.0, 0.0, 1.0),
  // });
  //2  fabric方式
  const material = new Cesium.Material({
    fabric: {
      type: "PolylineGlow",
      uniforms: {
        color: new Cesium.Color(1.0, 0.0, 0.0, 1.0),
        glowPower: 0.6,
        taperPower: 0.8,
      },
    },
  });
  let appearance = new Cesium.MaterialAppearance({
    material: material,
  });
  //4图元
  let primitives = new Cesium.Primitive({
    geometryInstances: instance,
    appearance: appearance,
  });
  //5添加到viewer
  viewer.scene.primitives.add(primitives);
});
</script>
<style scoped lang="less">
* {
  margin: 0;
  padding: 0%;
}
#cesiumContainer {
  width: 100%;
  height: 100%;
}
</style>
