<template>

  <div style="height: 350px;">
    <div class="info" style="height: 15%">
      <span>Center: {{ center }}</span>
      <span>Zoom: {{ zoom }}</span>
      <span>Bounds: {{ bounds }}</span>
    </div>
    <l-map
        style="height: 350px"
        :zoom="zoom"
        :maxZoom="maxZoom"
        :minZoom="minZoom"
        :center="center"
        @update:zoom="zoomUpdated"
        @update:center="centerUpdated"
        @update:bounds="boundsUpdated"
    >
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-geo-json :geojson="geojsonIsl" :options-style="styleFunction"></l-geo-json>

      <l-geo-json :geojson="geojson" :options-style="styleFunction"></l-geo-json>
      <l-polyline
          v-for="(item,ind) in this.disp"
          :key="ind"
          :color="randomColor(ind)"
          :lat-lngs=item>
      </l-polyline>
    </l-map>
  </div>
</template>

<script>
import {LMap, LTileLayer,LGeoJson,LPolyline} from 'vue2-leaflet';
export default {
  components: {
    LMap,
    LTileLayer,
    LGeoJson,
    LPolyline
  },
  props:{
    pathCoordsToSend: Map,
    colorMap:Map
  },
  data () {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
          '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 13,
      maxZoom:20,minZoom:7,
      center: [36.069082, 24.867382],
      bounds: null,
      geojson: null,
      geojsonIsl: null,
      disp: Object,
    };
  },
  async created() {
    const response = await fetch("/json/Abila.json");
    const response2 = await fetch("/json/Kronos_Island.json");
    const data = await response.json();
    const data2 = await response2.json();
    this.geojsonIsl = data2;
    this.geojson = data;
  },

  methods: {
    zoomUpdated (zoom) {
      this.zoom = zoom;
    },
    centerUpdated (center) {
      this.center = center;
    },
    boundsUpdated (bounds) {
      this.bounds = bounds;
    },
    randomColor(ind) {
      console.log("random color",this.colorMap.get(ind));
      return this.colorMap.get(ind);
    },
  },
  watch: {
    pathCoordsToSend(pathCoordsToSend){
      if(typeof pathCoordsToSend !== "undefined"){
        this.disp = Object.fromEntries(pathCoordsToSend);
        console.log("2DCP", this.disp);
      }else {
        this.disp = {}
      }
    },
    colorMap(colorMap){
      console.log("Color map in map", colorMap);
    }
  },
  computed: {
    styleFunction() {
      return () => {
        return {
          weight: 1,
          color: "rgba(105,105,105,0.94)",
          opacity: 1,
          pane: 'mapPane',
        };
      };
    },
  }
}
</script>