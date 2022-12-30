<template>
  <div id="app">
    <b-row id="main">    <dashboard/> </b-row>
  </div>
</template>
<script>

import dashboard from "@/components/Dashboad";
export default {
  name: 'App',
  components: {
    dashboard,
  },
  data() {
    return {
      selectedValue: 1,
    }
  },
}
</script>

<style>

b-row,b-col,b-container{
  margin:0;padding:0;
}


</style>
