<template>
  <VueDeckgl
      :layers="layers"
      :viewState="viewState"
      @click="nothing"
      @view-state-change="updateViewState"
      @getCursor="({isDragging}) => (isDragging ? 'grabbing' : (isHovering ? 'pointer' : 'grab'))"
      class="deck-class">
    <div id="map" ref="map"/>
  </VueDeckgl>
</template>

<script>
import {IconLayer} from "@deck.gl/layers";
import mapboxgl from "mapbox-gl";
import VueDeckgl from 'vue-deck.gl';

export default {
  name: "Map",
  components: {
    VueDeckgl
  },
  data() {
    return {
      isHovering: false,
      pathData: [
        {log: 7.686736, lat: 45.054847},
        {log: 7.685089, lat: 45.071217},
        {log: 8.48426, lat: 44.30459},
      ],
      accessToken: "pk.eyJ1IjoicG9zaWU5OCIsImEiOiJjbDV5MTVteXAwOHRoM2VwZDFlYzN4YTJuIn0.1rRyi4xUKIBqfnhfA9GfVQ",
      mapStyle: "mapbox://styles/posie98/cl7jhub3v005j14nfyksvuc9p",
      viewState: {
        latitude: 44.3072,
        longitude: 8.484106,
        zoom: 8,
        bearing: 0,
        pitch: 0
      },
    };
  },
  created() {
    this.map = null;
  },
  mounted() {
    this.map = new mapboxgl.Map({
      accessToken: this.accessToken,
      container: this.$refs.map,
      interactive: false,
      style: this.mapStyle || "mapbox://styles/posie98/cl7jhub3v005j14nfyksvuc9p",
      center: [this.viewState.longitude, this.viewState.latitude],
      zoom: this.viewState.zoom,
      pitch: this.viewState.pitch,
      bearing: this.viewState.bearing,
    });
  },
  methods: {
    updateViewState: function(viewState) {
      this.viewState = {
        ...viewState,
        transitionDuration: 700
      }
      this.map.jumpTo({
        center: [viewState.longitude, viewState.latitude],
        zoom: viewState.zoom,
        bearing: viewState.bearing,
        pitch: viewState.pitch
      });
    },
    nothing: function() {},
    printClick: function() {
      console.log("click!!");
    }
  },
  computed: {
    layers() {
      if (this.pathData.length === 0) {
        return [];
      } else {
        return [
          new IconLayer({
            id: 'icon-layer',
            data: this.pathData,
            iconAtlas: 'https://raw.githubusercontent.com/visgl/deck.gl-data/master/website/icon-atlas.png',
            iconMapping: {marker: {x: 0, y: 0, width: 128, height: 128, mask: true}},
            getIcon: () => 'marker',
            getPosition: (d) => [d.log, d.lat],
            getSize: () => 4,
            sizeScale: 10,
            getColor: [150, 123, 220],
            pickable: true,
            onClick: () => this.printClick(),
            onHover: () => (this.isHovering = true)
          })];
      }
    }
  }
}
</script>

<style scoped>
#map {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #e5e9ec;
  overflow: hidden;
}
</style>