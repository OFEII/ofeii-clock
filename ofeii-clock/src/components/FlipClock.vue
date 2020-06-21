<template>
  <div class="container">
    <div class="ofeii-clock">
      <div class="hour">
        <div class="hr" id="clock-hour" ref="clockHourRef"></div>
      </div>
      <div class="min">
        <div class="mn" id="clock-min" ref="clockMinRef"></div>
      </div>
      <div class="sec">
        <div class="sc" id="clock-sec" ref="clockSecRef"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      
    }
  },
  created() {
    this.timeRun()
  },
  methods: {
    timeRun(){
      const deg = 6
      const hr = document.querySelector('#hr')
      const mn = document.querySelector('#mn')
      const sc = document.querySelector('#sc')

      setInterval(() => {
        let day = new Date()
        let hh = day.getHours()*30
        let mm = day.getMinutes()*deg
        let ss = day.getSeconds()*deg

        this.$refs.clockHourRef.style.transform = `rotateZ(${hh+(mm/12)}deg)`
        this.$refs.clockMinRef.style.transform = `rotateZ(${mm}deg)`
        this.$refs.clockSecRef.style.transform = `rotateZ(${ss}deg)`

      });
    }
  },
  
}
</script>

<style lang="sass" scoped>
*
  margin: 0
  border: 1px solid #000

</style>