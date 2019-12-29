<template>
  <div class="pie-component">
    <table>
      <tr v-for="(value, name) in dataWithoutMonth" :key="value.month" @mouseover="yeet(name, value, $event)">
        <td>{{name}}:</td>
        <td>{{value}}</td>
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
    },
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
      const self = this;
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
        .on("mouseover", function(d,i) {
          self.pieMouseOver(d,i,this);
        })
        .on("mouseout", function(d,i) {
          self.pieMouseOut(d,i,this);
        })
    },
    pieMouseOver(d, i, context) {
      this.svg.classed("hover", true)
      select(context).classed("active", true)
    },
    pieMouseOut(d, i, context) {
      this.svg.classed("hover", false)
      select(context).classed("active", false )
    },
    yeet(name, value, e) {
      console.log(name, value, e)
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

<style lang="scss" > 
  svg.hover path {
    opacity: 0.5;
    transition: opacity 0.2s;
  }

  .active {
    opacity: 1 !important;
  }  
  
</style>