<template>
  <div class="box">
    <div
      id="container"
      style="width:100%; height:100%"
    ></div>
    <div class="btns">
      <div id="traffic"><button @click="traffic">打开实时路况</button></div>
      <div id="mark"><button @click="mark([103.977271, 30.633954])">增加标记点</button></div>
      <div id="addLine"><button @click="addLine">增加一条线路</button></div>
      <div id="addAnPolygon"><button @click="addAnPolygon">区域显示</button></div>
      <div id="threeDMode"><button @click="threeDMode">切换视角</button></div>
      <div id="getParams"><button @click="getParams">获取参数</button></div>
    </div>
    <div
      v-if="paramsList.length !== 0"
      class="params"
    >
      <div
        v-for="(item, index) in paramsList"
        :key="index"
      >
        <span>{{ item.name }}</span>{{ item.value }}
      </div>
    </div>
  </div>
</template>

<script>
import AMap from "AMap";
export default {
  name: "Home",
  components: {},
  data() {
    return {
      map: null,
      mapContainer: {},
      paramsList: []
    };
  },
  mounted() {
    this.init();
    this.$nextTick(() => {
      setInterval(this.getParams, 1000);
    });
  },
  methods: {
    init() {
      this.map = new AMap.Map("container", {
        center: [103.977271, 30.633954],
        resizeEnable: true,
        zoom: 16,
        zooms: [4, 20],
        mapStyle: "amap://styles/macaron",
        viewMode: "3D"
      });
      this.map.setFeatures(["road", "bg", "point", "building"]);
    },
    traffic() {
      let trafficLayer = new AMap.TileLayer.Traffic({
        zIndex: 10
      });
      this.map.add(trafficLayer);
    },
    mark(point) {
      let marker = new AMap.Marker({
        position: point
      });
      this.map.add(marker);
      return marker;
    },
    threeDMode() {
      // this.map.setViewMode = "2D";
      // this.map.setFitView();
    },
    getParams() {
      // 获取地图中的参数
      let paramsMapIndex = {};
      this.paramsList.forEach((item, index) => {
        paramsMapIndex[item.id] = index;
      });

      this.map.getCity(result => {
        let cityStr = `${result.province} ${result.city} ${result.district}`;
        if (paramsMapIndex && paramsMapIndex.hasOwnProperty("local"))
          this.paramsList[paramsMapIndex["local"]].value = cityStr;
        else
          this.paramsList.push({
            id: "local",
            name: "当前位置",
            value: cityStr
          });
      });
    },
    addLine() {
      let lineArr = [
        [103.977931, 30.647442],
        [103.978108, 30.644908],
        [103.980704, 30.63654],
        [103.97968, 30.636153],
        [103.976123, 30.634846]
      ];
      let polyline = new AMap.Polyline({
        path: lineArr, //设置线覆盖物路径
        strokeColor: "#A211C1", //线颜色
        strokeWeight: 3, //线宽
        strokeStyle: "solid" //线样式
      });
      this.map.add(polyline);
      // 为每个节点添加标记
      lineArr.forEach(item => {
        let marker = this.mark(item);
        this.addMarkInfo(
          marker,
          "<span style='color:#A211C1;font-weight:600;'>59路:</span>开往西部智谷公交站"
        );
      });
      // 调整视角
      this.map.setFitView();
      // 输出所有的marker
      console.log(this.map.getAllOverlays("marker"));
    },
    addMarkInfo(marker, info) {
      let $vm = this;
      let infoWindow = new AMap.InfoWindow({
        //创建信息窗体
        isCustom: true, //使用自定义窗体
        content:
          "<div style='border:1px solid #666;padding:4px 6px;background-color:#fff;'>" +
          info +
          "</div>", //信息窗体的内容可以是任意html片段
        offset: new AMap.Pixel(16, -45),
        closeWhenClickMap: true,
        showShadow: true
      });
      let onMarkerClick = e => {
        // 点击了
        infoWindow.open($vm.map, e.target.getPosition()); //打开信息窗体
      };
      marker.on("click", onMarkerClick); //绑定click事件
    },
    addAnPolygon() {
      let path = [
        [103.975721, 30.634454],
        [103.97578, 30.634519],
        [103.978113, 30.635405],
        [103.978285, 30.635349],
        [103.978902, 30.633591],
        [103.976338, 30.632451]
      ];

      let polygon = new AMap.Polygon({
        path: path,
        fillColor: "rgb(122,122,236)",
        fillOpacity: 0.3,
        borderWeight: 2,
        strokeColor: "red",
        strokeOpacity: 0.5,
        strokeStyle: "dashed",
        strokeDasharray: [10, 2, 10]
      });

      this.map.add(polygon);
    }
  }
};
</script>
<style scoped>
.box {
  width: 100%;
  height: 100%;
}
.btns {
  position: absolute;
  bottom: 0;
  right: 0;
  margin: 0 10px 10px 0;
}
.btns > div {
  float: right;
  padding: 0 5px;
}
.params {
  position: absolute;
  top: 0;
  left: 0;
  margin: 10px 0 0 10px;
  padding: 5px 10px;
  font-size: 10px;
  color: #fff;
  background-color: rgba(0, 0, 0, 0.45);
  border: 1px solid rgba(200, 200, 200, 0.85);
  max-width: 300px;
}
.params span {
  font-size: 8px;
  padding-right: 3px;
  color: #ddd;
}
.params span::after {
  content: ":";
}
</style>
