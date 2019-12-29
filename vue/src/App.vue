<template>
  <div id="app">

    <Pie :data="data" />

  </div>
</template>
<script>

import Pie from './components/Pie';

export default {
  name: 'app',
  components: { Pie },
  data() {
    return {
      data: {
        month: "januari",
        setup: 20,
        monthly: 43,
        usage: 30,
      },
    }
  },
  mounted() {
    // setInterval(()=> {
    //   this.data.push({
    //     month:  Math.random().toString(36).substring(2, 4) + Math.random().toString(36).substring(2, 4), //random string
    //     setup: Math.floor(Math.random() * 50),
    //     monthly: Math.floor(Math.random() * 50),
    //     usage: Math.floor(Math.random() * 50),
    //   })
    // }, 1000)
  }
}
</script>

<style lang="scss">
</style>
