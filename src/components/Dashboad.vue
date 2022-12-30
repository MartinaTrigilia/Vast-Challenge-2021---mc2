<template>

    <b-container id="board" class="board">

      <!-- NavBar with Calendar to select the day-->
       <b-row align-v="stretch" class="b-row pl-4 pr-4">
        <b-col class="b-col p-0" md="6" >
          <b-navbar id="navbar">
            <b-col><Calendar @get-day="filterDay"/></b-col>
          </b-navbar>
        </b-col>
      </b-row>

      <!-- Select Aggregation Level-->
      <b-row id="charts_row2" justify="left" >
        <b-col class="b-col" md="4">
          <b-form-select v-model="aggrTrans" size="sm" class="mb-3"  @change="onClickTrans">
            <option :value=true>Total Trans</option>
            <option :value=false>Total Price</option>
          </b-form-select>
        </b-col>
      </b-row>

      <!-- Stack BarChart and HeatMap-->
      <b-row id="charts_row2" align-v="stretch" class="b-row mt-5">
        <b-col class="b-col" md="6">
          <b-card-text><StackBarchart :aggregationType="aggrBarChartMap"></StackBarchart></b-card-text>
        </b-col>

        <b-col class="b-col" md="6">
          <Heatmap :matrix_loc="aggrHeatMap"></Heatmap>
        </b-col>
      </b-row>


    </b-container>
</template>

<script>

import StackBarchart from '@/components/StackBarchart.vue';
import Calendar from "@/components/Calendar";
import Heatmap from "@/components/Heatmap";

function findIntervalRange(hour){
  switch (hour){
    case '0','1':
      return '0-2';
    case '2','3':
       return '2-4';
    case '4','5':
      return '4-6';
    case '6','7':
      return '6-8';
    case '8','9':
      return '8-10'
    case '10','11':
      return  '10-12'
    case '12','13':
      return  '12-14'
    case '14','15':
      return  '14-16'
    case '16','17':
      return '16-18'
    case '18','19':
      return  '18-20'
    case '20','21':
     return  '20-22'
    default:
      return  '22-24'
  }
}
/* Import Credit and Loyalty Cards Data */

const d3 = require('d3')
console.log("D3 Module:", d3);

export default {
  name: 'mc2_board',
  components: {
    StackBarchart,
    Calendar,
    Heatmap
  },
  data() {
    return {
        dataPayments: Array,
        dataGPS: [],
        selectedDay: ' ',
        loc_amount: new Map(),
        loc_trans: new Map(),
        aggrBarChartMap: new Map(),
        hour_amount: new Map(),
        aggrHeatMap:new Map(),
        aggrTrans:true
    }
  },
  mounted() {

    /**Lettura e Parsing dei due data set */

     d3.csv('/csv/df_cc_loyalty.csv')
          .then((rows) => {
            let cc_loyalty_cards = rows
                .map((row) => {

                  //from string to date
                  const dateParse = d3.timeParse("%Y-%m-%d");
                  const dateF = dateParse(row.date)

                  /*Add a String to indicate method of payment of employee*/
                  let method;
                  if(row.last4ccnum === 'No')
                    method = 'Loyalty'
                  else if (row.loyaltynum === 'No')
                    method = 'Card'
                  else
                    method = 'Both'
                  let range = findIntervalRange(row.Hour);
                  return {
                    Date: dateF,
                    location: row.location,
                    price: +row.price,
                    last4ccnum: row.last4ccnum,
                    loyaltynum: row.loyaltynum,
                    date: row.date,
                    hour: row.Hour,
                    minutes: row.Minutes,
                    method: method,
                    rangeHour: range,
                  };
                });
            // Let's calculate some aggregation for the stack bar chart
            console.log("cc_loyalty_cards filterd: ", cc_loyalty_cards);
            this.dataPayments = cc_loyalty_cards;
            // count transaction per location and method
            this.loc_trans = d3.rollup(cc_loyalty_cards, v => v.length, d => d.location,d => d.method)
            const l_t_All = d3.rollup(cc_loyalty_cards, v => v.length, d => d.location)
            this.loc_trans.forEach((value, key) => {
              value.set("All",l_t_All.get(key));
            } )
            // total amount per location and method
            this.loc_amount = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price), d => d.location,d => d.method)
            const l_a_All = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price), d => d.location)
            this.loc_amount.forEach((value, key) => {
              value.set("All",l_a_All.get(key));
            } )

            this.aggrBarChartMap = this.loc_trans;
            console.log("Location Amount: ", this.loc_amount);

            // total amount per range hour and location
            this.hour_amount = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price),d => d.rangeHour, d => d.location)
            this.hour_trans = d3.rollup(cc_loyalty_cards, v => v.length, d => d.rangeHour,d => d.location)
            this.aggrHeatMap  = this.hour_trans;
            console.log("Location Hour: ", this.hour_amount);

          });

    /* Import Gps and Car Cards Data */
    d3.csv('/csv/df_gps_car.csv')
        .then((rows) => {
          const gps_car = rows
              .map((row,i) => {
                return {
                  CarID: row.CarID,
                  CurrentEmploymentType:  row.CurrentEmploymentType,
                  CurrentEmploymentTitle: row.CurrentEmploymentTitle,
                  fullname: row.FullName,
                  lat: row.lat,
                  long: row.long,
                  date: row.date,
                  hour: row.Hour,
                  minutes: row.Minutes,
                  seconds: row.Seconds,
                  timestamp_id: i
                };
              });
          console.log("gps_car: ", gps_car);
        });
  },
  computed:{
  },
  methods:{

    /*This method is called when a user select an option of aggregation
    * and changes Stack BarChart and HeatMap*/
    onClickTrans(){
      if(this.aggrTrans){
        this.aggrHeatMap = this.hour_trans;
        this.aggrBarChartMap = this.loc_trans;
      }
      else {
        this.aggrHeatMap = this.hour_amount;
        this.aggrBarChartMap = this.loc_amount;
      }
    },

    /* Given a Date(day) filters Stack BarChart and HeatMap */
    filterDay(day) {
      this.selectedDay = day;

      //filter bar chart stack per day selected
      let cc_loyalty_cards = this.dataPayments;
      if (day) {
        cc_loyalty_cards =  cc_loyalty_cards.filter(d => d.date == day)
      }
      this.loc_trans = d3.rollup(cc_loyalty_cards, v => v.length, d => d.location,d => d.method)
      const l_t_All = d3.rollup(cc_loyalty_cards, v => v.length, d => d.location)
      this.loc_trans.forEach((value, key) => {
        value.set("All",l_t_All.get(key));
      } )
      this.loc_amount = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price), d => d.location,d => d.method)
      const l_a_All = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price), d => d.location)
      this.loc_amount.forEach((value, key) => {
        value.set("All",l_a_All.get(key));
      } )
      console.log("Changed day in the main dash", this.selectedDay);
      // filter heatmap per day selected...
      this.hour_amount = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price),d => d.rangeHour, d => d.location)
      this.hour_trans = d3.rollup(cc_loyalty_cards, v => v.length, d => d.rangeHour,d => d.location)
      if(this.aggrTrans){
        this.aggrHeatMap = this.hour_trans;
        this.aggrBarChartMap = this.loc_trans;
      }
      else{
        this.aggrHeatMap = this.hour_amount;
        this.aggrBarChartMap = this.loc_amount;
      }
    },
  },
  watch: {

  },
}
</script>

<style>
#board{
  margin-top:30px;
}

.b-col{
  margin-top:5px;
}
b-navbar{
  margin-top:1px;
}
#map-container {
  height: fit-content;
  width: 100%;
  max-width: 100%;
  position: relative;
}

#abila_map {
  max-width: 100%;
  max-height: 100%;
}

#img_map {
  opacity: 0.9;
  position: absolute;
  top: 0;
  left: 0;
  z-index: -1;
  height: 100%;
  width:100%;
  padding-left:10px;
  padding-right:10px;
}

#cars_data{
  overflow:scroll;
  overflow-x:hidden;
  height:auto;
  max-height:500px;
}

svg g.trajectories path {
  stroke: blue
}

svg g.trajectories path.selected {
  stroke: red
}

#location_list{
  overflow:scroll;
  overflow-x:hidden;
  height:315px;
}

#cards_details {
  overflow:scroll;
  overflow-x:hidden;
  height:315px;
}

.generic_details{
  background-color: #f2f2f2;
  border-radius: 5px;
}

</style>