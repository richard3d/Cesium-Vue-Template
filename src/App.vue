/**
 * Created by richard.becker on 11/6/18.
 * The top level/root component for the application
 */
import '../assets/app.css'


<template>
<div style="background-color:lightgrey;">
  <div>
    <h1>Test App</h1>
  </div>
  <div style ="width:100%;">
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
    rowClicked(event) {
      this.$eventBus.$emit("selectEntity", event.name);
      this.$eventBus.$emit("rowSelected", event);
    },
    cesiumOnMouseOver(event) {

    },
    cesiumOnEntitySelected(event) {

    }
  }
}
</script>
