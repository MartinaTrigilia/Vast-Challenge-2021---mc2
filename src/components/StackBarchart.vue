<template>
    <div class="Stack">
      <vue-plotly  class="" :data="data" :layout="layout" :options="options" @click="triggerClick"/>
      <p style="margin-left: 20px">Stacked Bar Chart - Change aggregation level and click on stack chart to see
        <br>transaction detail</p>
    </div>
</template>
<script>
import VuePlotly from '@statnett/vue-plotly';

export default {

  name: 'StackBarchart',
    components: {
        VuePlotly
    },
    props:{
      aggregationType: Map,
    },
    data() {
      let unique_loc = ["Brew've Been Served",
        "Hallowed Grounds",
        "Hippokampos",
        "Kalami Kafenion",
        "Katerina Cafè",
        "Ouzeri Elian",
        "Frydos Autosupply n' More",
        "Coffee Shack",
        "Abila Zacharo",
        "Gelatogalore",
        "Albert's Fine Clothing",
        "Guy's Gyros",
        "Coffee Cameleon",
        "Shoppers' Delight",
        "General Grocer",
        "Octavio's Office Supplies",
        "Bean There Done That",
        "Brewed Awakenings",
        "Jack's Magical Beans",
        "Carlyle Chemical Inc.",
        "Abila Airport",
        "Kronos Pipe and Irrigation",
        "Maximum Iron and Steel",
        "U-Pump",
        "Nationwide Refinery",
        "Stewart and Sons Fabrication",
        "Abila Scrapyard",
        "Frank's Fuel",
        "Chostus Hotel",
        "Ahaggo Museum",
        "Desafio Golf Course",
        "Roberts and Sons",
        "Kronos Mart",
        "Daily Dealz"]

      return {
            el:'#app',
            data: [{
                x: unique_loc,
                y:[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
                type: 'bar',
                name: "Both",
                marker: {color: '#448'}
            },
              {
                x: unique_loc,
                y:[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
                type: 'bar',
                name: "Loyalty",
                marker: {color: '#88C'}
              },
              {
                x: unique_loc,
                y:[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
                type: 'bar',
                name: "Credit",
                marker: {color: '#CCF'}
              },
              {
                x: unique_loc,
                y:[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
                type: 'line',
                name: "Moving Avg",
                marker: {color: '#ffccd0'}
              }
            ],
            layout: {
                height:550,
                showlegend: true,
                margin: {
                  t: -1,
                },
                legend: {
                  x: 0.9,
                  xanchor: 'right',
                  y: 1
                },
                barmode: "stack",
                xaxis: {
                  automargin: true
                },
                yaxis: {
                  automargin: true
                },
            },
            options: {
                displayModeBar: false,
                scrollZoom: false,
                responsive: true
            },
        }
    },
  methods: {
    triggerClick (value) {
      let stack = {"cc": [], "label" : ' '};
      value.points.forEach((s) =>  {
        stack["cc"].push(s.fullData.name);
        stack["label"] = s.label;
      })
      stack["cc"] = stack["cc"].filter(function(item) {
        return item !== 'Moving Avg'
      })
      const creditIndex = stack["cc"].indexOf("Credit");
      if (creditIndex !== -1) {
        stack["cc"][creditIndex] = 'Card';
      }
      this.$emit('get-stackbar', stack);

    }
  },
    watch: {
      aggregationType(loc_map) {
        console.log("Loc Map in the Bar Chart Component", loc_map);
        const mapSort1 = new Map([...loc_map.entries()].sort((a, b) => b[1].get('All') - a[1].get('All')));
        let y_both = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
        let y_card = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
        let y_loy = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
        let y_avg = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
        let x_axis = [];
        let i = 0;
        mapSort1.forEach((value,key) => {
          x_axis[i] = key;
            value.forEach((v,k) => {
              if(k=="Both")
                y_both[i]=v;
              if(k=="Card")
                y_card[i]=v;
              if(k=="Loyalty")
                y_loy[i]=v;
              if(k=="Avg")
                y_avg[i]=v;
          } )
          i++;
        } )

        this.data[0].y = y_both;
        this.data[1].y = y_loy;
        this.data[2].y = y_card;
        this.data[3].y = y_avg;

        this.data[0].x = x_axis;
        this.data[1].x = x_axis;
        this.data[2].x = x_axis;
        this.data[3].x = x_axis;

        console.log("Y BOTH Map in the Bar Chart Component",  this.data[0].x);
        console.log("Y BOTH Map in the Bar Chart Component",  this.data[1].x);
        console.log("Y BOTH Map in the Bar Chart Component",  this.data[2].x);
        console.log("Y BOTH Map in the Bar Chart Component",  this.data[3].x);
      },
        deep: true
    },
}


</script>

<style scoped>
.Stack{
  width: 700px;
  padding-rigth: 50px;
}
</style>

