/**
 * Created by richard.becker on 3/11/19.
 * The top level/root component for the application
 */
import '../assets/app.css'

<template>
<div>
  <div>
    <h1>Cesium-Vue Template App</h1>
  </div>
  <div>
      <CesiumComponent style ="display:inline-block;" :on-mouse-over="cesiumOnMouseOver" :on-entity-selected="cesiumOnEntitySelected"/>
  </div>
</div>
</template>

<script>

import CesiumComponent from './components/CesiumComponent.vue'

export default {
  components: {
    CesiumComponent
  },
  created () {

  },
  methods: {
    cesiumOnMouseOver(event) {
    },
    cesiumOnEntitySelected(event) {
    }
  }
}
</script>
