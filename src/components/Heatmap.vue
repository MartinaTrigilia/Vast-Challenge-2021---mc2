<template>
  <div class="HeatMap">
    <vue-plotly class="" :data="data" :layout="layout" :options="options"/>
  </div>
</template>

<script>
import VuePlotly from '@statnett/vue-plotly';

let location_map = new Map();
location_map.set("Brew've Been Served",0);
location_map.set("Hallowed Grounds",1);
location_map.set("Hippokampos",2);
location_map.set("Kalami Kafenion",3);
location_map.set("Katerina Cafè",4);
location_map.set("Ouzeri Elian",5);
location_map.set("Frydos Autosupply n' More",6);
location_map.set("Coffee Shack",7);
location_map.set("Abila Zacharo",8);
location_map.set("Gelatogalore",9);
location_map.set("Albert's Fine Clothing",10);
location_map.set("Guy's Gyros",11);
location_map.set("Coffee Cameleon",12);
location_map.set("Shoppers' Delight",13);
location_map.set("General Grocer",14);
location_map.set("Octavio's Office Supplies",15);
location_map.set("Bean There Done That",16);
location_map.set("Brewed Awakenings",17);
location_map.set("Jack's Magical Beans",18);
location_map.set("Carlyle Chemical Inc.",19);
location_map.set("Abila Airport",20);
location_map.set("Maximum Iron and Steel",21);
location_map.set("U-Pump",22);
location_map.set("Nationwide Refinery",23);
location_map.set("Stewart and Sons Fabrication",24);
location_map.set("Abila Scrapyard",25);
location_map.set("Frank's Fuel",26);
location_map.set("Chostus Hotel",27);
location_map.set("Ahaggo Museum",28);
location_map.set("Desafio Golf Course",29);
location_map.set("Roberts and Sons",30);
location_map.set("Kronos Mart",31);
location_map.set("Daily Dealz",32);

let range_map = new Map();
range_map.set("0-2",0);
range_map.set("2-4",1);
range_map.set("4-6",2);
range_map.set("6-8",3);
range_map.set("8-10",4);
range_map.set("10-12",5);
range_map.set("12-14",6);
range_map.set("14-16",7);
range_map.set("16-18",8);
range_map.set("18-20",9);
range_map.set("20-22",10);
range_map.set("22-24",11);

function getIndex(key,map) {
  return map.get(key);
}

let unique_loc = [];
location_map.forEach((value, key) => { unique_loc.push(key) } )
const xValues = unique_loc;
const yValues = ['0- 2', '2- 4', '4- 6', '6- 8','8- 10','10- 12','12- 14','14- 16','16- 18','18- 20','20- 22','22- 24'];
let zValues = [
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0., 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
  [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00]
];
export default {
  name: 'Heatmap',
  components: {
    VuePlotly
  },
  props:{
    matrix_loc: Map,
  },
  data() {
    return {
      data: [{
        x: xValues,
        y: yValues,
        z: zValues,
        type: 'heatmap',
        colorscale: 'Portland',
        hoverongaps: false
      },
      ],
      layout: {
        height: 450,
        width: 700,
        title: '',
        annotations: [],
        xaxis: {
          height: 100,
          ticks: { height: 500},
          side: 'bottom'
      },
        yaxis: {
          ticks: '',
          ticksuffix: ' ',
          autosize: false
        },
      },
      options: {
        displayModeBar: false,
      },

    }
  },
  watch: {
    // whenever data changes, this function will run
    matrix_loc(matrix_loc){
      console.log("Heatmap",matrix_loc);
      let z_axis = [
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0., 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00],
        [0, 0, 0, 0, 0,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00,0.00, 0.00, 0.00,0.00, 0, 0.00, 0.00, 0.00, 0.00, 0.00]
      ];
      matrix_loc.forEach((submap, range) => {
        submap.forEach((price, loc) => {
          let index_loc = getIndex(loc,location_map);
          let index_rg = getIndex(range,range_map);
          z_axis[index_rg][index_loc] = price;
        } )
      } )
      console.log("zValues", z_axis);
      this.data[0].z = z_axis;
      console.log("z data",this.data[0].z);
    },
    deep: true
  }
}
</script>

<style scoped>
.HeatMap{
  height: 600px;
  width: 600px;
  /*marginRight: 40px;*/
}
</style>
