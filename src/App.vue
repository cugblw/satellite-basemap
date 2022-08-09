<template>
  <div id="mapContainer" class="SatelliteImageService"></div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import 'leaflet/dist/leaflet.css';
import L from 'leaflet';
import './assets/leaflet-side-by-side';
// import 'leaflet-side-by-side';

export default defineComponent({
  name: "SatelliteImageService",

  mounted() {
    let map = L.map("mapContainer", {
      center: [36.06, 103.78],
      zoom: 15,
    });

    const OSM = L.tileLayer(
      "http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
    );

    const ImageServiceBefore = L.tileLayer(
      "http://127.0.0.1:8002/service/satellite/before_clear/{z}/{x}/{y}"
    );

    const ImageServiceAfter = L.tileLayer(
      "http://127.0.0.1:8002/service/satellite/after_clear/{z}/{x}/{y}"
    );

    var overlayMaps = {
      BeforeClear: ImageServiceBefore,
      AfterClear: ImageServiceAfter,
    };

    OSM.addTo(map);
    ImageServiceBefore.addTo(map);
    ImageServiceAfter.addTo(map);

    // L.control.layers(overlayMaps).addTo(map);


    var tiles = this.showtilegrid();

    tiles.addTo(map);
    // @ts-ignore
    L.control.sideBySide(ImageServiceBefore, ImageServiceAfter).addTo(map);
  },

  methods: {
    showtilegrid() {
      class TileGrid extends L.GridLayer {
        createTile(coords: L.Coords) {
          var tile = L.DomUtil.create("canvas", "leaflet-tile");
          var ctx: any = tile.getContext("2d");
          var size = this.getTileSize();
          tile.width = size.x;
          tile.height = size.y;

          var nwPoint = coords.scaleBy(size);

          ctx.fillStyle = "rgb(44, 51, 51, 0.3)";
          ctx.fillRect(0, 0, size.x, 30);
          ctx.fillStyle = "#E8F9FD";
          ctx.font = "normal bold 16px Arial";
          ctx.fillText(
            "x: " + coords.x + ", y: " + coords.y + ", zoom: " + coords.z,
            20,
            20
          );

          ctx.strokeStyle = "#125B50";
          ctx.beginPath();
          ctx.moveTo(0, 0);
          ctx.lineTo(size.x - 1, 0);
          ctx.lineTo(size.x - 1, size.y - 1);
          ctx.lineTo(0, size.y - 1);
          ctx.lineWidth = 1.0;
          ctx.closePath();
          ctx.stroke();

          return tile;
        }
      }
      var tiles = new TileGrid();

      return tiles;
    },
  },

  // components: {},
});
</script>

<style>
#mapContainer {
  width: 100vw;
  height: 100vh;
}
</style>
