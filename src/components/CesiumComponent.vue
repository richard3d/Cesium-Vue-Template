<template>
<div style="background-color:lightblue;">
    <div id="toolbar">
        <table>
            <tbody><tr>
                <td>Animation Slider</td>
                <td>
                    <input type="range" min="0" max="100" step="1" data-bind="value: pixelRange, valueUpdate: 'input'">
                    <input type="text" size="1" data-bind="value: pixelRange">
                </td>
            </tr>
            <tr>
              <input type="file"
                     id="gltf_file" name="gltf_file"
                     accept="*"/>
            </tr>
            </tbody>
        </table>
    </div>
    <div id = "cesiumContainer" style="width: 1080px; height:720px"/>


</div>
</template>

<script>
import 'cesium/Widgets/widgets.css';
var Cesium = require('cesium/Cesium');
import { AnimationParser, AnimationPlayer, Animation, AnimationTrack, AnimationKey, LOOP_TYPE } from '../gltf_animation'


export default {
  data() {
    return {
      viewer:{},
      entity: {},
      animation_player: {}
    }
  },
  props:
    ['onMouseOver', 'onEntitySelected']
  ,
  created() {

  },

  mounted() {
    const self = this;
    // add event handler for loading file
    document.getElementById('gltf_file').addEventListener('change', this.loadFile, false);

    //create alias for eventbus and callbacks so we don't have to deal with 'this'
    var eventBus = this.$eventBus;
    eventBus.$on('selectEntity', this.getEntity);
    eventBus.$on('cesiumLoadCzml', this.loadCzml);

    var onMouseOverCallback = this.onMouseOver;
    var onEntitySelectedCallback = this.onEntitySelected;

    var viewer = new Cesium.Viewer('cesiumContainer');
    this.viewer = viewer;

    var viewModel = {
      pixelRange: 10
    };

    Cesium.knockout.track(viewModel);

    var toolbar = document.getElementById('toolbar');
    Cesium.knockout.applyBindings(viewModel, toolbar);

    function subscribeParameter(name) {
      Cesium.knockout.getObservable(viewModel, name).subscribe(
        function(newValue) {
          self.animation_player.stop();
          self.animation_player.setPercent(newValue/100.0);
        }
      );
    }

    subscribeParameter('pixelRange');
  },
  methods : {
    getEntity(name) {
      var entity = this.viewer.dataSources
      for(var i = 0; i < this.viewer.dataSources.length; i++) {
        var entity = this.viewer.dataSources.get(i).entities.getById(name.toLowerCase());
        if(entity != null) {
          this.viewer.selectedEntity = entity;
          return;
        }
      }
    },
    loadCzml(czml) {
      Cesium.CzmlDataSource.load(czml).then(function (source)  {
        //console.log(source);
        var dataSource = source;
        this.viewer.dataSources.add(dataSource);
      }.bind(this));
    },
    loadKml(kml) {
      this.viewer.dataSources.add(Cesium.KmlDataSource.load(kml));
    },

    async loadFile(e) {
      var gltf_file = e.target.files[0];
      console.log(gltf_file);
      var position = Cesium.Cartesian3.fromDegrees(-123.0744619, 44.0503706, 10000);
      var entity = this.viewer.entities.add({
        //Use our computed positions
        position : position,

        //Load the Cesium plane model to represent the entity
        model : {
          uri : '../assets/gltf/'+gltf_file.name,
          nodeTransformations : {
            "joint3" : new Cesium.Quaternion(1,1,1,1)
          },
          runAnimations: false
        }
      });
      this.viewer.trackedEntity = entity;
      this.entity = entity;

      let animation_set = await AnimationParser.parseAnimationSetFromFile('../assets/gltf/'+gltf_file.name);
      let player = new AnimationPlayer(animation_set, this.entity, 30);
      player.loop_type = LOOP_TYPE.LOOP;
      player.speed = -10;
      this.animation_player = player;
      //player.play("animation_0");


      /*var modelMatrix = Cesium.Transforms.eastNorthUpToFixedFrame(
          Cesium.Cartesian3.fromDegrees(-75.62898254394531, 40.02804946899414, 0.0));
      var model = this.viewer.scene.primitives.add(Cesium.Model.fromGltf({
          url : '../assets/gltf/'+gltf_file.name,
          modelMatrix : modelMatrix,
          scale : 200.0
      }));

      Cesium.when(model.readyPromise).then(function(model) {
        console.log(model.getNode("node_0"));
        console.log(model._runtime['nodesByName']);

      });*/


    }
  },
}
</script>