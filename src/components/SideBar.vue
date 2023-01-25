<template>
  <div id="navbar">
    <b-sidebar id="sidebar-1" title="Transaction Details" bg-variant="secondary" text-variant="Dark" class="text-center" shadow>
      <b-row>
        <b-col cols="12">
          <b-card-group id="cardGroup"
                        :per-page="perPage"
                        :current-page="currentPage"
                        v-for="(item,ind) in this.trans"
                        :key="ind"
                        deck>
              <b-card border-variant="dark" bg-variant="light" class="text-center">
                <b-card-text> {{ item.method }}</b-card-text>
              </b-card>
          </b-card-group>
        </b-col>
      </b-row>

      <b-row>
        <b-col md="6" class="my-1">
          <b-pagination
              @change="onPageChanged"
              :total-rows="totalRows"
              :per-page="perPage"
              v-model="currentPage"
              class="my-0" />
        </b-col>
      </b-row>
    </b-sidebar>
  </div>
</template>

<script>

export default {
  name: "SideBar",
  props:{
    transactions: Map,
  },
  data(){
    return{
      trans: new Map(),
      paginatedItems: this.trans,
      perPage: 3,
      totalRows: this.trans.length,
      currentPage: 1
    }
  },
  computed: {
    pageCount() {
      let l = this.totalRows,
          s = this.perPage;
      return Math.floor(l / s);
    }
  },
  mounted(){
    this.paginate(this.perPage, 1)
  },
  methods: {
    paginate (page_size, page_number) {
      let itemsToParse = this.trans
      this.paginatedItems = itemsToParse.slice(page_number * page_size, (page_number + 1) * page_size);
    },
    onPageChanged(page){
      this.paginate(this.perPage, page - 1)
    }
  },
  watch: {
    transactions(transactions){
      let values =[...transactions.values()]
      if(values.length == 2)
       values =  values[0].concat(values[1]);
      if(values.length == 3){
        values =  values[0].concat(values[1]);
        values =  values[0].concat(values[1]);
      }
      this.trans = values;
      console.log("trans in nav bar",this.trans);
    }
  },
}
</script>

<style>
#b-card-text{
  color:black;
}
</style>
