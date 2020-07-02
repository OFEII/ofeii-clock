<template>
  <div class="home">
    <OfeiiClock></OfeiiClock>
  </div>
</template>

<script>
// @ is an alias to /src
import OfeiiClock from '@/components/OfeiiClock.vue'

export default {
  name: 'Home',
  components: {
    OfeiiClock
  }
}
</script>
<style lang="scss">
*{
  font-family:'Microsoft YaHei';
}
</style>
