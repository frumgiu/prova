<template>
  <div class="deck-container">
    <div id="map" ref="map"></div>
    <canvas id="deck-canvas" ref="canvas"></canvas>
  </div>
</template>

<script>
import { Deck } from "@deck.gl/core";
import mapboxgl from "mapbox-gl";
import {IconLayer} from "@deck.gl/layers";

export default {
  name: "AppTest",
  data() {
    return {
      isHovering: false,
      accessToken: "pk.eyJ1IjoicG9zaWU5OCIsImEiOiJjbDV5MTVteXAwOHRoM2VwZDFlYzN4YTJuIn0.1rRyi4xUKIBqfnhfA9GfVQ",
      mapStyle: "mapbox://styles/posie98/cl7jhub3v005j14nfyksvuc9p",
      pathData: [
        {log: 7.686736, lat: 45.054847},
        {log: 7.685089, lat: 45.071217},
        {log: 8.48426, lat: 44.30459},
      ],
      viewState: {
        latitude: 44.3072,
        longitude: 8.484106,
        zoom: 8,
        bearing: 0,
        pitch: 0
      },
    }
  },
  created() {
    this.map = null;
    this.deck = null;
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

    this.deck = new Deck({
      canvas: this.$refs.canvas,
      width: "100%",
      height: "100%",
      initialViewState: this.viewState,
      controller: true,
      getCursor: ({isDragging}) => (isDragging ? 'grabbing' : (this.isHovering ? 'pointer' : 'grab')),
      layers: [
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
        })
      ],
      onViewStateChange: ({ viewState }) => {
        this.map.jumpTo({
          center: [viewState.longitude, viewState.latitude],
          zoom: viewState.zoom,
          bearing: viewState.bearing,
          pitch: viewState.pitch,
        });
      },
    });
  },
  methods: {
    printClick: function() {
      console.log("Click!");
    }
  }
}
</script>

<style scoped>
.deck-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
#map {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #e5e9ec;
  overflow: hidden;
}
#deck-canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>