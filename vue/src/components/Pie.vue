<template>
  <div class="pie-component">
    <table>
      <tr v-for="(value, name) in dataWithoutMonth" :key="value.month">
        {{name}}: {{value}}
        <!-- <td>{{row.month}}</td> -->
        <!-- <td>{{data}}</td> -->
      </tr>
    </table>
    
    <svg ref="svg" id="svg"></svg>
  </div>
</template>
<script>

import { select } from 'd3-selection';
import { pie, arc } from 'd3-shape'; 

export default {
  name: 'Pie',
  props: ["data"],
  data() {
    return {
      svg: null,
      margin: { top: 10, right: 10, bottom: 10, left: 10 },
      width: 400,
      height: 400,
      colors: [
        'rgba(16, 32, 88, 1)',
        'rgba(63, 150, 130, 1)',
        'rgba(224, 224, 224, 1)',
      ]
    }
  },
  computed: {
    dataWithoutMonth() {
      return (({ month, ...o }) => o)(this.data) // remove b and c
    },
    calcPie() {
      return pie()
        .sort(null)
        .value(d => d)
    },
    calcArc() {
      return arc()
        .innerRadius(0)
        .outerRadius(Math.min(this.width, this.height) / 2 - 1)
    }
  },
  mounted() {
    this.svg = select("#svg")
      .attr("height", this.height)
      .attr("width", this.width)
      .attr("viewBox", [-this.width / 2, -this.height / 2, this.width, this.height]);

    this.render();
  },
  methods: {
    render() {
      this.svg.append("g")
          .attr("stroke", "white")
        .selectAll("path")
        .data(this.calcPie(
          Object.keys(this.data)
            .filter(d => d !== 'month')
            .map(d => this.data[d])
        ))
        .join("path")
          .attr("fill", (d,i) => this.colors[i % this.colors.length])
          .attr("d", this.calcArc)
    }
  },
  watch: {
    data(newData) {
      console.log(newData);
      this.render();
    }
  }
}
</script>

<style lang="scss">
</style>
