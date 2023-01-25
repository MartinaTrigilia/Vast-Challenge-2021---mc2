<template>

    <b-container id="board" class="board">

      <!-- NavBar with Calendar to select the day-->
       <b-row align-v="stretch" class="b-row pl-4 pr-4">
        <b-col class="b-col p-0" md="6" >
          <NavBar @get-day="filterDay"/>
        </b-col>
      </b-row>

      <!-- Select an Aggregation Level-->
      <b-row id="aggr_level" justify="center" >
        <b-col/>
        <b-col class="b-col" md="4" style="margin-top: 7%" >
          <b-form-select id="tooltip-target-1" v-model="aggrTrans" size="sm" class="mb-3"  @change="onClickTrans">
            <option :value=true>Transitions</option>
            <option :value=false>Price</option>
          </b-form-select>
          <b-tooltip target="tooltip-target-1" triggers="hover">
            Select an aggregation measure!
          </b-tooltip>
        </b-col>
        <b-col/>
      </b-row>

      <!-- Stack BarChart-->

      <b-row id="BarHeat" align-v="stretch" class="b-row mt-5">
      <b-col class="b-col" md="6"  style="margin-top: -5%">
        <StackBarchart style="z-index: 99" class="stackbar" @get-stackbar="toggleSideBar" :aggregationType="aggrBarChartMap"></StackBarchart>
      </b-col>

        <!-- SideBar -->
      <SideBarCard id="sidebar-1" title="Sidebar" shadow :transactions="transactions"></SideBarCard>

        <!-- HeatMap -->
      <b-col id= "byday" class="b-col" md="6" style="margin-top: -40%; margin-left: 54%">
        <b-tabs v-if="isAllday" align="center" style="z-index: 1" content-class="mt-3">
          <b-tab  title="By Day" active>
            <Heatmap :matrix_loc="this.aggrHeatMap" ></Heatmap>
          </b-tab>
          <b-tab id = "byhour" title="By Hours">
            <Heatmap :matrix_loc="this.aggrHeatMapHour" ></Heatmap>
          </b-tab>
        </b-tabs>
        <Heatmap v-else :matrix_loc="this.aggrHeatMapHour" style="margin-top: 10%;" ></Heatmap>
      </b-col>
      </b-row>

      <!-- Employers List -->

      <b-row id="EmployersList" align-v="stretch" class="b-row mt-5">
        <b-col class="b-col" md="6">
          <EmployerList
              @get-employers="filterEmployers"
              :employers_lst="this.employers"
              :selected_all="this.selected_all"
          ></EmployerList>
        </b-col>
      </b-row>

      <!-- Abila Map -->
      <b-row id="AbilaMap" align-v="stretch" class="b-row mt-5">
        <b-col class="b-col">
          <AbilaMap :pathCoordsToSend="this.pathCoordsToSend" :colorMap="this.colorMap"/>
        </b-col>

      </b-row>

    </b-container>
</template>

<script>

import StackBarchart from '@/components/StackBarchart.vue';
import Heatmap from "@/components/Heatmap";
import EmployerList from "@/components/EmployerList";
import AbilaMap from "@/components/AbilaMap";
import {crossfilter} from "crossfilter/crossfilter";
import NavBar from "@/components/NavBar";
//import SideBar from "@/components/SideBar";
import SideBarCard from "@/components/SideBarCard";


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
let avg_mvg_a = new Map();
let avg_mvg_t = new Map();

export default {
  name: 'mc2_board',
  components: {
    NavBar,
    StackBarchart,
    Heatmap,
    EmployerList,
    AbilaMap,
    SideBarCard
  },
  data() {
    return {
        dataPayments: Array,
        pathCoords: new Map(),
        pathCoordsToSend: new Map(),
        dayCoords: new Map(),
        empCoords: new Map(),
        selectedDay: ' ',
        loc_amount: new Map(),
        loc_trans: new Map(),
        aggrBarChartMap: new Map(),
        hour_amount: new Map(),
        hour_trans: new Map(),
        day_amount: new Map(),
        day_trans: new Map(),
        ht_amount: new Map(),
        ht_trans: new Map(),
        aggrHeatMap:new Map(),
        aggrHeatMapHour:new Map(),
        aggrTrans:true,
        EmpType: Array,
        employers: new Map(),
        employers_sel: [],
        selected_all: false,
        colorMap: new Map(),
        group_trans: new Map(),
        transactions: [],
    }},
  mounted() {

    /** Lettura e Parsing dei due data set */

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
            console.log("cc_loyalty_cards filterd", cc_loyalty_cards);
            this.dataPayments = cc_loyalty_cards;
            // count transaction per location and method
            this.loc_trans = d3.rollup(cc_loyalty_cards, v => v.length, d => d.location,d => d.method)
            this.group_trans = d3.group(cc_loyalty_cards, d => d.location,d => d.method);
            const l_t_All = d3.rollup(cc_loyalty_cards, v => v.length, d => d.location)
            avg_mvg_t = d3.rollup(cc_loyalty_cards, v => v.length, d => d.location, d=>d.date)
            avg_mvg_t.forEach((day_v_map,loc) => {
              let sum = 0;
              day_v_map.forEach((v) => {
                sum = sum + v;
              })
              avg_mvg_t.set(loc,Math.round(sum/day_v_map.size));
            })
            console.log("ALL", avg_mvg_t)
            this.loc_trans.forEach((value, key) => {
              value.set("All",l_t_All.get(key));
              value.set("Avg",avg_mvg_t.get(key));
            } )
            // total amount per location and method
            this.loc_amount = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price), d => d.location,d => d.method)
            const l_a_All = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price), d => d.location)
            avg_mvg_a = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price), d => d.location, d=>d.date);
            avg_mvg_a.forEach((day_v_map,loc) => {
              let sum = 0;
              day_v_map.forEach((v) => {
                sum = sum + v;
              })
              avg_mvg_a.set(loc,Math.round(sum/day_v_map.size));
            })
            this.loc_amount.forEach((value, key) => {
              value.set("All",l_a_All.get(key));
              value.set("Avg",avg_mvg_a.get(key));
            } )

            this.aggrBarChartMap = this.loc_trans;
            console.log("Location Amount: ", this.group_trans);

            // total amount per range hour and location
            this.hour_amount = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price),d => d.rangeHour, d => d.location)
            this.hour_trans = d3.rollup(cc_loyalty_cards, v => v.length, d => d.rangeHour,d => d.location)
            this.day_trans = d3.rollup(cc_loyalty_cards, v => v.length, d => d.date,d => d.location)
            this.day_amount = d3.rollup(cc_loyalty_cards,  v => d3.sum(v, d => d.price), d => d.date,d => d.location)

            this.aggrHeatMap  = this.day_trans;
            this.aggrHeatMapHour = this.hour_trans;
            console.log("Location Hour: ", this.hour_trans);
          });

    function assignColorToPath(colorMap,ind){
      const randomColor = Math.floor(Math.random()*16777215).toString(16);
      let color= "#" + randomColor;
      colorMap.set(ind,color);
      return colorMap.get(ind);
    }

    function getDuration(h_start, min_start, h_last, min_last) {
      return Math.abs(h_last-h_start)*60 + Math.abs(min_last - min_start);
    }
    function formatTime(element) {
      if(element < 10)
        return "0"+(element).toString()
    else
      return element.toString();
    }
    /* Import Gps and Car Cards Data */
    d3.csv('/csv/df_gps_car.csv')
        .then((rows) => {
          const gps_car = rows
              .map((row,i) => {
                let range = findIntervalRange(row.Hour);
                return {
                  CarID: row.CarID,
                  CurrentEmploymentType:  row.CurrentEmploymentType,
                  CurrentEmploymentTitle: row.CurrentEmploymentTitle,
                  fullname: row.FullName,
                  lat: row.lat,
                  long: row.long,
                  coordinate: row.lat + " " + row.long,
                  date: row.date,
                  hour: row.Hour,
                  minutes: row.Minutes,
                  seconds: row.Seconds,
                  timestamp_id: i,
                  path_id : row.PathID,
                  rangeHour:range
                };
              });
          let gps_car_CC = crossfilter(gps_car);
          let empTypeD = gps_car_CC.dimension(d => { return d.CurrentEmploymentType});

          // all types of employment -- unique values
          console.log("unique employer type", empTypeD.group().reduceCount().all().map(d => d.key));
          console.log("gps_car: ", gps_car);
          let Employers_Set = new Set();
          gps_car.forEach((el) => {
            Employers_Set.add(JSON.stringify({Fullname: el.fullname, CarID: parseInt(el.CarID),
            Title: el.CurrentEmploymentTitle, Type:el.CurrentEmploymentType}))})
          console.log("Employer Set: ", Employers_Set);
          let emp_list = []
          function parseSetElements(entry) {
            emp_list.push(JSON.parse(entry));
          }
          Employers_Set.forEach(parseSetElements);
          console.log("Employer List",emp_list);
          let emp_listByType = d3.group(emp_list, d => d.Type)

          console.log("emloyers",emp_listByType);
          this.employers = emp_listByType;

          /*gps by path*/

          const gpsByPath = d3.group(gps_car, v => v.path_id)
          let path_coord = new Map();

          /* create a map that link each path with its coordinates list */
          gpsByPath.forEach((el,path) => {
            let coord_list = [];
                el.forEach((coord) =>{
                  coord_list.push([coord.lat,coord.long]);
                    }
                )
            let prop = {"CarID": (el[0].CarID).toString(), "Employer": el[0].fullname,"PathId":(el[0].path_id).toString(),
              "hour_start":formatTime(el[0].hour),
              "min_start": formatTime(el[0].minutes),
              "hour_last": formatTime(el[el.length-1].hour),
              "min_last":formatTime(el[el.length-1].minutes),
              "duration":getDuration(el[0].hour,el[0].minutes,el[el.length-1].hour,el[el.length-1].minutes),
            }
              path_coord.set(path,[prop,coord_list]);
          }
          )
          this.pathCoords = path_coord;
          //this.pathCoordsToSend = path_coord;
          [...this.pathCoords.keys()].forEach(key => {
            assignColorToPath(this.colorMap, key);
          })
          console.log("Lettura gps", this.pathCoords);

          /*gps by day and emp*/
          const gpsByDay = d3.group(gps_car, v => v.date, v=>v.fullname)
          let day_coord = new Map();
          gpsByDay.forEach((dip_map,day) => {
            let map_dip_el = new Map();
            dip_map.forEach((arr,dip) => {
              let dip_list = new Set();
              arr.forEach((ele) => {
                dip_list.add(ele.path_id);
              })
              map_dip_el.set(dip,dip_list);
            })
            day_coord.set(day,map_dip_el);
          })
          this.dayCoords = day_coord;
          console.log("GPS DATA  IN DASH",day_coord);

          const gpsByEmp = d3.group(gps_car, v=>v.fullname, v => v.date);
          let emp_coord = new Map();
          gpsByEmp.forEach((day_map,emp) => {
            let map_day_el = new Map();
            day_map.forEach((arr,day) => {
              let day_list = new Set();
              arr.forEach((ele) => {
                day_list.add(ele.path_id);
              })
              map_day_el.set(day,day_list);
            })
            emp_coord.set(emp,map_day_el);
          })
          this.empCoords = emp_coord;
          console.log("emp_coord", emp_coord);
        });
    },
  computed:{
    isAllday(){
      return this.selectedDay == ' ' || this.selectedDay==0 ? true : false;
    }
  },
  methods:{

    /*This method is called when a user select an option of aggregation
    * and changes Stack BarChart and HeatMap*/
    onClickTrans(){
      if(this.aggrTrans){
        this.aggrHeatMap = this.day_trans;
        this.aggrHeatMapHour = this.hour_trans;
        this.aggrBarChartMap = this.loc_trans;
      }
      else {
        this.aggrHeatMap = this.day_amount;
        this.aggrHeatMapHour = this.hour_amount;
        this.aggrBarChartMap = this.loc_amount;
      }
    },
    filterDay(day) {
      this.selectedDay = day;

      /* Given a Date(day) filters Stack BarChart and HeatMap */

      //filter bar chart stack per day selected
      let cc_loyalty_cards = this.dataPayments;
      /*check if day is empty*/
      if (day) {
        cc_loyalty_cards =  cc_loyalty_cards.filter(d => d.date == day)
      }
      this.group_trans = d3.group(cc_loyalty_cards, d => d.location,d => d.method);
      this.loc_trans = d3.rollup(cc_loyalty_cards, v => v.length, d => d.location,d => d.method)
      const l_t_All = d3.rollup(cc_loyalty_cards, v => v.length, d => d.location)
      this.loc_trans.forEach((value, key) => {
        value.set("All",l_t_All.get(key));
        value.set("Avg",avg_mvg_t.get(key));
      } )
      this.loc_amount = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price), d => d.location,d => d.method)
      const l_a_All = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price), d => d.location)
      this.loc_amount.forEach((value, key) => {
        value.set("All",l_a_All.get(key));
        value.set("Avg",avg_mvg_a.get(key));
      } )
      console.log("Changed day in the main dash", this.selectedDay);

      // filter heatmap per day selected
      this.hour_amount = d3.rollup(cc_loyalty_cards, v => d3.sum(v, d => d.price),d => d.rangeHour, d => d.location)
      this.hour_trans = d3.rollup(cc_loyalty_cards, v => v.length, d => d.rangeHour,d => d.location)
      if(this.aggrTrans){
        this.aggrHeatMapHour = this.hour_trans;
        this.aggrBarChartMap = this.loc_trans;
      }
      else{
        this.aggrHeatMapHour = this.hour_amount;
        this.aggrBarChartMap = this.loc_amount;
      }

      /* Given a Date(day) filters Stack GPS and Map */
      if(day) {
        console.log("Giorno c'è");
        /* case in which there is no employer select yet*/
        if (this.employers_sel.length == 0) {
          this.selected_all = true;
        } else {
          this.selected_all = false;
          console.log("DAY sono nel els", this.dayCoords.get(day));
          let all_path = [];
          this.employers_sel.forEach((k) => {
            console.log("k", k.Fullname);
            let dayC = this.dayCoords.get(day).get(k.Fullname);
            console.log("dayC else", dayC);
            dayC = Array.from(dayC);
            dayC.forEach((el) => {
              all_path.push(el);
            })
          })
          let path_coords = new Map(this.pathCoords);
          for (let k of path_coords.keys()) {
            if (!(all_path.includes(k))) {
              path_coords.delete(k);
            }
          }
          this.pathCoordsToSend = path_coords;
        }
      } else{
        console.log("Giorno non c'è",this.pathCoordsToSend.clear());
        //this.pathCoordsToSend = this.pathCoordsToSend.clear();
        //this.pathCoordsToSend
        this.filterEmployers(this.employers_sel);
      }
    },
    toggleSideBar(stack){
      console.log("stack in dash", this.group_trans, stack);
      let transaction = new Map(this.group_trans);
      transaction = transaction.get(stack['label']);
      console.log("transaction in toggle", transaction);
      transaction.forEach((array_details,method) => {
        if(!(stack['cc'].includes(method))){
          transaction.delete(method)
        }
      })
      let values = [...transaction.values()]
      if(values.length == 2)
        values =  values[0].concat(values[1]);
      if(values.length == 3){
        values =  values[0].concat(values[1]);
        values =  values[0].concat(values[1]);
      }
      this.transactions = values;
      console.log("transaction in toggle", typeof(this.transactions), transaction);
      this.$root.$emit('bv::toggle::collapse', 'sidebar-1')
    },
    /* Given a List of selected Employers filter the Map */
    filterEmployers(employers_list){
      console.log("DIPENDENTI NELLA DASH",employers_list);
      this.employers_sel = employers_list;
      console.log("selectedDay", this.selectedDay.length);
      /* Given a list of employers filters Stack GPS and Map */
      if(this.selectedDay == ' ' || this.selectedDay==0){
        console.log("selectedDay", typeof (this.selectedDay));
        this.selected_all = false;
        let all_path = [];
        employers_list.forEach((k) => {
          console.log("k",k.Fullname);
          let empC = this.empCoords.get(k.Fullname);
          empC.forEach((array_path) => {
            array_path.forEach((el) => {
              all_path.push(el);
            })
          })})
          let path_coords = new Map(this.pathCoords);
          console.log("DAY SEL COORD DASH ", this.pathCoords);
          for (let k of path_coords.keys()) {
            if (!(all_path.includes(k))) {
              path_coords.delete(k);
            }
          }
          this.pathCoordsToSend = path_coords;
      }
      else {
        this.selected_all = false;
        let all_path = [];
        console.log("DIPENDENTI NELLA DASH 2", employers_list);
        employers_list.forEach((k) => {
          console.log("k",k.Fullname);
          if(this.empCoords.get(k.Fullname).has(this.selectedDay)){
            let empC = this.empCoords.get(k.Fullname).get(this.selectedDay);
            console.log("empC",empC);
            empC = Array.from(empC);
            empC.forEach((el) => {
              all_path.push(el);
            })
          }})
        let path_coords = new Map(this.pathCoords);
        console.log("DAY SEL COORD DASH ", this.pathCoords);
        for (let k of path_coords.keys()) {
          if (!(all_path.includes(k))) {
            path_coords.delete(k);
          }
        }
        console.log("DAY SEL COORD DASH 2", path_coords);
        this.pathCoordsToSend = path_coords;
      }
    },
  },
}
</script>

<style>
#board{
  margin-top:30px;
  margin-left: 0px;
  padding-left: 0px;
  padding-right: 0px;
}

.b-col{
  margin-top:5px;
}
b-navbar{
  margin-top:1px;
}
#BarHeat{
  margin-top:5px;
  margin-bottom:20px;
}
#EmployersList
{
  margin-left: 50px;
}
#AbilaMap
{
  margin-left: 50px;
}
</style>