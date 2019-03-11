<template>
<div style="background-color:lightblue; ">
    <div id="toolbar">
    </div>
    <div id = "cesiumContainer" style="width: 1080px; height:720px"/>
</div>
</template>

<script>
import 'cesium/Widgets/widgets.css';
var Cesium = require('cesium/Cesium');

export default {
  data() {
    return {
      viewer:{},
    }
  },
  props:
    ['onMouseOver', 'onEntitySelected']
  ,
  created() {

  },
  mounted() {

    var onMouseOverCallback = this.onMouseOver;
    var onEntitySelectedCallback = this.onEntitySelected;

    var viewer = new Cesium.Viewer('cesiumContainer');
    this.viewer = viewer;

  },
  methods : {

  },
}
</script>