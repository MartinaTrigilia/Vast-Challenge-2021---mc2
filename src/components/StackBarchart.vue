<template>
    <div>
      <vue-plotly  class="" :data="data" :layout="layout" :options="options"/>
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

            data: [
              {
                label: 'Moving Avg',
                avg:[30,40,20,25,45,30,30,30,40,50,60,70,70,10,10,10,23,24,35,56,35,22,22,45,67,35,24,25,26,25,53,24],
                borderColor: 'rgb(75, 192, 192)',
                tension:0.5,
                type: 'line'
            }, {
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
              }
            ],
            layout: {
                height:350,
                showlegend: true,
                barmode: "stack",
                title: {
                    text: "",
                    font: {
                        family: 'Quicksand, monospace',
                        size: 10
                    },
                    xref: 'paper',
                    x: 0.05,
                },
                xaxis: {
                    title: {
                        text: '',
                        font: {
                            family: 'Quicksand, monospace',
                            size: 10,
                            color: '#7f7f7f'
                        },
                      ticklen: 0.5
                    },
                },
                yaxis: {
                    title: {
                        text: 'Count',
                        font: {
                            family: 'Quicksand, monospace',
                            size: 10,
                            color: '#7f7f7f'
                        }
                    },
                },
            },
            options: {
                displayModeBar: false,
                scrollZoom: false,
                responsive: true
            },
        }
    },

    watch: {
      // whenever data changes, this function will run
      aggregationType(loc_map) {
        console.log("Loc Map in the Bar Chart Component", loc_map);
        const mapSort1 = new Map([...loc_map.entries()].sort((a, b) => b[1].get('All') - a[1].get('All')));
        let y_both = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
        let y_card = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
        let y_loy = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
        let x_axis = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
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
          } )
          i++;
        } )

        console.log("Y BOTH Map in the Bar Chart Component", y_both);
        console.log("Y CARD Map in the Bar Chart Component", y_card);
        console.log("Y LOY Map in the Bar Chart Component", y_loy);
        //da cambiare c'è qualcosa che nn va nel sorting


        this.data[0].y = y_both;
        this.data[1].y = y_loy;
        this.data[2].y = y_card;

        this.data[0].x = x_axis;
        this.data[1].x = x_axis;
        this.data[2].x = x_axis;

        console.log("Avg line", this.data[3].data);

        this.layout.title.text = "Popularity of All location";
        },
        deep: true
    },
}
</script>

