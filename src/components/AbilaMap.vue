<template>

  <div style="height: 350px;margin-bottom: 200px">
    <div class="info" style="height: 15%">
      <span>This map shows the various employee paths and has to be filtered by day and/or by one or more employees.
        <br> Apply the chosen filters and then hover over a specific path to see the information associated with that path. </span>
    </div>
    <l-map
        style="height: 450px; z-index: 1"
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
          :lat-lngs=item[1]>

        <l-tooltip><div class="tt path">
          <span class="car_id">Date</span>: {{item[0]["Day"]}}<br>
          <span class="employer">Employer</span>: {{item[0]["Employer"]}} <br>
          <span class="employer_typ">Employer Type</span>: {{item[0]["Type"]}} <br>
          <span class="car_id">CarId</span>: {{item[0]["CarID"]}}<br>
          <span class="path_id">PathID </span>: {{item[0]["PathId"]}} <br>
          <span class="start_time">Starting Time </span>: {{item[0]["hour_start"]}}:{{item[0]["min_start"]}} <br>
          <span class="end_time">Ending Time </span>: {{item[0]["hour_last"]}}:{{item[0]["min_last"]}} <br>
          <span class="duration">Duration </span>: {{item[0]["duration"]}} minutes<br>
        </div>
        </l-tooltip>

      </l-polyline>

      <l-marker
          v-for="(item,ind) in this.loc_coordinates"
          :key="ind"
          :lat-lng="item.coord">
        <l-tooltip>{{item.name}}</l-tooltip>
        <l-icon
            icon-url="json/marker-icon.png" >
        </l-icon>
      </l-marker>


    </l-map>
  </div>
</template>

<script>
import {LMap, LTileLayer,LGeoJson,LPolyline,LTooltip,LMarker,LIcon} from 'vue2-leaflet';
export default {
  components: {
    LMap,
    LTileLayer,
    LGeoJson,
    LPolyline,
    LTooltip,
    LMarker,
    LIcon
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
      maxZoom:17,minZoom:7,
      center: [36.069082, 24.867382],
      bounds: null,
      geojson: null,
      geojsonIsl: null,
      disp: Object,
      loc_coordinates:[
        {
          name: "Ahaggo Museum",
          coord: [36.0759652,24.8769882]
        },
        {
          name: "Bean There Done That",
          coord: [36.0820936,24.8515639]
        },
        {
          name: "Frank's Fuel",
          coord: [36.0724428,24.8412562]
        },
        {
          name: "Abila Scrapyard",
          coord: [36.074089,24.846560]
        },
        {
          name: "Kronos Mart",
          coord: [36.065795,24.847389]
        },
        {
          name: "Roberts and Sons",
          coord: [36.063472,24.852270]
        },
        {
          name: "General Grocer",
          coord: [36.0617819,24.8581400]
        },
        {
          name: "General Grocer",
          coord: [36.0617819,24.8581400]
        },
        {
          name: "Gelatogalore",
          coord: [36.05978258,24.85804657]
        },
        {
          name: "Albert's Fine Clothing",
          coord: [36.0750225,24.8564542]
        }
      ]
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