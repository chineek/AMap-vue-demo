<template>
  <div class="box">
    <div
      id="container"
      style="width:100%; height:100%"
    ></div>
  </div>
</template>

<script>
import AMap from "AMap";
export default {
  name: "Home",
  components: {},
  mounted() {
    this.init();
  },
  methods: {
    init() {
      let map = new AMap.Map("container", {
        center: [116.397428, 39.90923],
        resizeEnable: true,
        zoom: 10,
        mapStyle: "amap://styles/21c3933c0904c12ca3eb1fc601ee8a71"
      });
    }
  }
};
</script>
<style scoped>
.box {
  width: 100%;
  height: 100%;
}
</style>