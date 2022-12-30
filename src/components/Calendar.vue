<template>
  <div>
    <b-form-datepicker id="datepicker-buttons" reset-button label-reset-button="Clear Date"
        placeholder="Choose a date" v-model="value" :no-key-nav=hideHeader
                       :date-info-fn="dateClass"  :min="min" :max="max" :initial-date="min"
                :hide-header=hideHeader @input="setDay(value)" locale="en">

    </b-form-datepicker>
  </div>
</template>
<script>

//wrapper for b-calendar component
export default {
  name: "selectDay",
  data() {
    const minDate = new Date('01/06/2014')
    // 2 weeks after
    const maxDate = new Date('01/19/2014')

    return {
      value: '',
      min: minDate,
      max: maxDate,
      hideHeader:true
    }
  },
  methods: {
    dateClass(ymd, date) {
      const day = date.getDate()
      return day >= 6 && day <= 19 ? 'table-info' : ''
    },
    setDay(day) {
      this.value = day;
      this.$emit('get-day', day);
      console.log("Date Selected", this.value);
    }
  }
}
</script>

<style scoped>

</style>