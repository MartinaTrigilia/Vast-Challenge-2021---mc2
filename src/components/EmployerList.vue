<template>
  <div>
      <b-card title="" header-tag="header" footer-tag="footer">

        <template #header>
          <h6 style="text-align: center;" class="mb-0">Select to filter the GPS Map</h6>
        </template>
        <template>
          <div>
            <b-tabs content-class="mt-3">
              <b-tab title="Engineering" active>
                <b-row><b-col md="4"><b>Engineering</b></b-col>
                  <b-col>
                    <b-button  v-if="eng_sel.length<eng_len" variant="info" size="sm" @click="selectAll('Engineering',true)">Select All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Select All</b-button>
                    <b-button  v-if="eng_sel.length>=1" variant="info" size="sm" @click="clearAll('Engineering')">Unselect All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Unselect All</b-button>
                  </b-col>
                </b-row>
                <template>
                  <div>
                    <b-table id="t_eng" striped hover
                             :items="employers_lst.get('Engineering')"
                             :fields="fields"
                             :per-page="perPage"
                             :current-page="currentPage_eng"
                             small>
                      <template v-slot:cell(select)="{ item }">
                        <b-btn size="sm" v-if="((eng_sel.filter(e => e.Fullname === item.Fullname).length==0))"
                               variant="info" @click="addItem(item,'Engineering')">Select</b-btn>
                        <b-btn size="sm" v-else variant="info" @click="deleteItem(item,'Engineering')">Unselect</b-btn>
                      </template>
                    </b-table>
                  </div>
                  <b-pagination
                      v-model="currentPage_eng"
                      :total-rows="eng_len"
                      :per-page="perPage"
                      aria-controls="t_eng"
                  ></b-pagination>
                </template>
              </b-tab>
              <b-tab title="Executive">
                <b-row><b-col md="4"><b>Executive</b></b-col>
                  <b-col>
                    <b-button  v-if="exec_sel.length<exec_len" variant="info" size="sm" @click="selectAll('Executive',true)">Select All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Select All</b-button>
                    <b-button  v-if="exec_sel.length>=1" variant="info" size="sm" @click="clearAll('Executive')">Unselect All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Unselect All</b-button>
                  </b-col>
                </b-row>
                <b-row>
                  <template>
                    <div>
                      <b-table id="t_exec" striped hover
                               :items="employers_lst.get('Executive')"
                               :fields="fields"
                               :per-page="perPage"
                               :current-page="currentPage_exec"
                               small>
                        <template v-slot:cell(select)="{ item }">
                          <b-btn size="sm" v-if="((exec_sel.filter(e => e.Fullname === item.Fullname).length==0))"
                                 variant="info" @click="addItem(item,'Executive')">Select</b-btn>
                          <b-btn size="sm" v-else variant="info" @click="deleteItem(item,'Executive')">Unselect</b-btn>
                        </template>
                      </b-table>
                    </div>
                  </template>
                </b-row>
              </b-tab>
              <b-tab title="Facilities">
                <b-row><b-col md="4"><b>Facilities</b></b-col>
                  <b-col>
                    <b-button  v-if="fac_sel.length<fac_len" variant="info" size="sm" @click="selectAll('Facilities',true)">Select All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Select All</b-button>
                    <b-button  v-if="fac_sel.length>=1" variant="info" size="sm" @click="clearAll('Facilities')">Unselect All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Unselect All</b-button>
                  </b-col>
                </b-row>
                <b-row>
                  <template>
                    <div>
                      <b-table id="t_fac" striped hover
                               :items="employers_lst.get('Facilities')"
                               :fields="fields"
                               :per-page="perPage"
                               :current-page="currentPage_fac"
                               small>
                        <template v-slot:cell(select)="{ item }">
                          <b-btn size="sm" v-if="((fac_sel.filter(e => e.Fullname === item.Fullname).length==0))"
                                 variant="info" @click="addItem(item,'Facilities')">Select</b-btn>
                          <b-btn size="sm" v-else variant="info" @click="deleteItem(item,'Facilities')">Unselect</b-btn>
                        </template>
                      </b-table>
                    </div>
                  </template>
                </b-row>
              </b-tab>
              <b-tab title="Information Technology">
                <b-row><b-col md="4"><b>Information Technology</b></b-col>
                  <b-col>
                    <b-button  v-if="IT_sel.length<IT_len" variant="info" size="sm" @click="selectAll('Information Technology',true)">Select All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Select All</b-button>
                    <b-button  v-if="IT_sel.length>=1" variant="info" size="sm" @click="clearAll('Information Technology')">Unselect All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Unselect All</b-button>
                  </b-col>
                </b-row>
                <b-row>
                  <template>
                    <div>
                      <b-table id="t_IT" striped hover
                               :items="employers_lst.get('Information Technology')"
                               :fields="fields"
                               :per-page="perPage"
                               :current-page="currentPage_IT"
                               small>
                          <template v-slot:cell(select)="{ item }">
                            <b-btn size="sm" v-if="((IT_sel.filter(e => e.Fullname === item.Fullname).length==0))"
                                   variant="info" @click="addItem(item,'Information Technology')">Select</b-btn>
                            <b-btn size="sm" v-else variant="info" @click="deleteItem(item,'Information Technology')">Unselect</b-btn>
                          </template>
                      </b-table>
                    </div>
                  </template>
                </b-row>
              </b-tab>
              <b-tab title="Security">
                <b-row><b-col md="3"><b>Security</b></b-col>
                  <b-col>
                    <b-button  v-if="sec_sel.length<sec_len" variant="info" size="sm" @click="selectAll('Security',true)">Select All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Select All</b-button>
                    <b-button  v-if="sec_sel.length>=1" variant="info" size="sm" @click="clearAll('Security')">Unselect All</b-button>
                    <b-button  v-else variant="info" disabled size="sm">Unselect All</b-button>
                  </b-col>
                </b-row>
                <b-row>
                  <template>
                    <div>
                      <b-table id="t_sec" striped hover
                               :items="employers_lst.get('Security')"
                               :fields="fields"
                               :per-page="perPage"
                               :current-page="currentPage_sec"
                               small>
                        <template v-slot:cell(select)="{ item }">
                            <b-btn size="sm" v-if="((sec_sel.filter(e => e.Fullname === item.Fullname).length==0))"
                                   variant="info" @click="addItem(item,'Security')">Select</b-btn>
                            <b-btn size="sm" v-else variant="info" @click="deleteItem(item,'Security')">Unselect</b-btn>
                        </template>
                      </b-table>
                    </div>
                    <b-pagination
                        v-model="currentPage_sec"
                        :total-rows="sec_len"
                        :per-page="perPage"
                        aria-controls="t_sec"
                    ></b-pagination>
                  </template>
                </b-row>
              </b-tab>
            </b-tabs>
          </div>
        </template>

      </b-card>
  </div>
</template>
<script>
export default {
  name: "EmployerList",
  props:{
    employers_lst: Map,
    selected_all: Boolean,
    refresh: Boolean
  },
    data() {
      return {
        perPage: 6,
        currentPage_sec: 1,
        currentPage_eng: 1,
        currentPage_fac:1,
        currentPage_exec:1,
        currentPage_IT:1,
        currentPage_all: 1,
        employers_selected: [],
        sec_sel: [],
        eng_sel: [],
        fac_sel: [],
        IT_sel: [],
        exec_sel:[],
        exec_len:0,
        IT_len:0,
        fac_len:0,
        sec_len:0,
        eng_len:0,
        fields: ["Fullname", "CarID", "Title","select"],
        layout: {
          height:351,
          annotations: []
        }
      }
},
  watch: {
    // whenever data changes, this function will run
    employers_lst(employers) {
      console.log("employers In Component Child", this.employers_lst);
      this.employers_lst.get("Engineering").forEach( (item)=>{
        if (this.eng_sel.filter(e => e.Fullname === item.Fullname).length > 0) {
          console.log("item incluso",item);
        }
        else console.log("non lo include", item);
      })
      console.log("Current Page", employers.get("Engineering").length);
      console.log("eng sel",this.eng_sel);
      this.exec_len = employers.get("Executive").length;
      this.eng_len = employers.get("Engineering").length;
      this.IT_len = employers.get("Information Technology").length;
      this.fac_len = employers.get("Facilities").length;
      this.sec_len = employers.get("Security").length;
      this.currentPage_IT = 1;
      this.currentPage_fac = 1;
      this.currentPage_eng  = 1;
      this.currentPage_sec  = 1;
      this.currentPage_exec  = 1;
    },
    selected_all(selected_all){
      console.log("select all nel dipen");
      console.log("eng_sel", this.eng_sel);
      if(selected_all){
        console.log("selected all", selected_all);
        let types = ['Security','Executive','Information Technology','Facilities','Engineering'];
        types.forEach((t) => {
          this.selectAll(t);
        })
      }
    },
    refresh(refresh){
      console.log("refresh nel dipen");
      if(refresh){
        let types = ['Security','Executive','Information Technology','Facilities','Engineering'];
        types.forEach((t) => {
          this.clearAll(t,true);
        })
        this.$emit('get-refresh');
      }
    }
  },
  methods: {
    addItem(item,type) {
      console.log("item",item);
      switch (type){
        case 'Security':
          this.sec_sel.push(item);
          break;
        case 'Executive':
          this.exec_sel.push(item);
          break;
        case 'Information Technology':
          this.IT_sel.push(item);
          break;
        case 'Facilities':
          this.fac_sel.push(item);
          break;
        case 'Engineering':
          this.eng_sel.push(item);
          break;
      }
      this.employers_selected.push(item);
      const emp = this.employers_selected;
      this.$emit('get-employers', emp);
    },
    deleteItem(item,type) {
      switch (type){
        case 'Security':
          this.sec_sel.splice(this.sec_sel.indexOf(item), 1);
          break;
        case 'Executive':
          this.exec_sel.splice(this.exec_sel.indexOf(item), 1);
          break;
        case 'Information Technology':
          this.IT_sel.splice(this.IT_sel.indexOf(item), 1);
          break;
        case 'Facilities':
          this.fac_sel.splice(this.fac_sel.indexOf(item), 1);
          break;
        case 'Engineering':
          this.eng_sel.splice(this.eng_sel.indexOf(item), 1);
          break;
      }
      /*remove element for filter graphs*/
      this.employers_selected.splice(this.employers_selected.indexOf(item), 1);
      const emp = this.employers_selected;
      this.$emit('get-employers', emp);
    },
    selectAll(type,send){
      console.log("sono stato chiamato",type);
      switch (type){
        case 'Security':
          this.employers_lst.get('Security').forEach((element) => {
            if(!this.sec_sel.includes(element)){
              this.sec_sel.push(element);
              this.employers_selected.push(element);
            }
          })
          break;
        case 'Executive':
          this.employers_lst.get('Executive').forEach((element) => {
            if(!this.exec_sel.includes(element)){
              this.exec_sel.push(element);
              this.employers_selected.push(element);
              console.log("emp selected",this.employers_selected);

            }
          })
          break;
        case 'Information Technology':
          this.employers_lst.get('Information Technology').forEach((element) => {
            if(!this.IT_sel.includes(element)){
              this.IT_sel.push(element);
              this.employers_selected.push(element);
              console.log("emp selected",this.employers_selected);

            }
          })
          break;
        case 'Facilities':
          this.employers_lst.get('Facilities').forEach((element) => {
            if(!this.fac_sel.includes(element)){
              this.fac_sel.push(element);
              this.employers_selected.push(element);
              console.log("emp selected",this.employers_selected);

            }
          })
          break;
        case 'Engineering':
          this.employers_lst.get('Engineering').forEach((element) => {
            if(!this.eng_sel.includes(element)){
              this.eng_sel.push(element);
              this.employers_selected.push(element);
              console.log("emp selected",this.employers_selected);
            }
          })
          break;
      }
      if(send){
        const emp = this.employers_selected;
        this.$emit('get-employers', emp);
      }
    },
    clearAll(type){
      switch (type){
        case 'Security':
          this.employers_lst.get('Security').forEach((element) => {
            if(this.sec_sel.includes(element)){
              const index = this.sec_sel.indexOf(element);
              this.sec_sel.splice(index, 1);
              this.employers_selected.splice(this.employers_selected.indexOf(element), 1);
            }
          })
          break;
        case 'Executive':
          this.employers_lst.get('Executive').forEach((element) => {
            if(this.exec_sel.includes(element)){
              const index = this.exec_sel.indexOf(element);
              this.exec_sel.splice(index, 1);
              this.employers_selected.splice(this.employers_selected.indexOf(element), 1);
            }
          })
          break;
        case 'Information Technology':
          this.employers_lst.get('Information Technology').forEach((element) => {
            if(this.IT_sel.includes(element)){
              const index = this.IT_sel.indexOf(element);
              this.IT_sel.splice(index, 1);
              this.employers_selected.splice(this.employers_selected.indexOf(element), 1);
            }
          })
          break;
        case 'Engineering':
          this.employers_lst.get('Engineering').forEach((element) => {
            if(this.eng_sel.includes(element)){
              const index = this.eng_sel.indexOf(element);
              this.eng_sel.splice(index, 1);
              this.employers_selected.splice(this.employers_selected.indexOf(element), 1);
            }
          })
          break;
        case 'Facilities':
          this.employers_lst.get('Facilities').forEach((element) => {
            if(this.fac_sel.includes(element)){
              const index = this.fac_sel.indexOf(element);
              this.fac_sel.splice(index, 1);
              this.employers_selected.splice(this.employers_selected.indexOf(element), 1);
            }
          })
          break;
      }
      const emp = this.employers_selected;
      this.$emit('get-employers', emp);
    }
  }
}
</script>

<style scoped>

</style>