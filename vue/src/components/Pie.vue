<template>
  <div class="pie-component">
    <table>
      <tr v-for="(value, name, index) in dataWithoutMonth" 
          :key="value.month" 
          @mouseover="tableMouseOver(name, value, index, $event)"
          @mouseleave="tableMouseOut(name, value, index, $event)"
        >
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
      margin: { top: 20, right: 20, bottom: 20, left: 20 },
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
      return (({ month, ...o }) => o)(this.data) //all minus 'month'
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
      // .attr("height", this.height)
      // .attr("width", this.width)
      .attr("viewBox", [
        -(this.width + this.margin.left + this.margin.right) / 2,
        -(this.height + this.margin.top + this.margin.bottom) / 2,
        this.width + this.margin.left + this.margin.right,
        this.height + this.margin.top + this.margin.bottom
      ]);

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
          // .attr("id", d=> {console.log(d)})
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
    tableMouseOver(name, value, index, e) {
      this.svg.classed("hover", true)
      this.svg
        .select(`path:nth-of-type(${index + 1})`)
        .classed("active", true)
      console.log(name, value, index, e)
    },
    tableMouseOut(name, value, index, e) {
      this.svg.classed("hover", false)
      this.svg.selectAll(`path`).classed("active", false)
    },
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
  svg path {
    transition: opacity 0.2s, transform 0.2s;
  }

  svg.hover path {
    opacity: 0.5;
  }

  .active {
    transform: scale(1.04);
    opacity: 1 !important;
  }  
  
</style>
