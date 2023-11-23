<template>
  <div ref="resizeRef" class="h-full w-full">
    <div id="kenya_map" class="960"></div>
  </div>
</template>

<script setup>
import * as d3 from 'd3'
import { onMounted } from 'vue'
import useResizeObserver from './use/resizeObserver'

const { scale, title, subtitle } = defineProps({
  scale: {
    type: Number,
    default: 600
  },

  title: {
    type: String,
    default: null
  },

  subtitle: {
    type: String,
    default: null
  }
})

const { resizeRef, resizeState } = useResizeObserver()

// Load external data and boot
const drawMap = async () => {
  const { width: w, height: h } = resizeState.dimensions
  console.log(w, h, 'LOG')
  // Map and projection
  const path = d3.geoPath()
  const projection = d3
    .geoMercator()
    .scale(scale)
    .center([36, 0])
    .translate([w / 5, h / 2])

  // Data and color scale
  let data = new Map()
  const colorScale = d3
    .scaleThreshold()
    .domain([0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100])
    .range(d3.schemeOranges[9])

  const svgMap = d3.select('#kenya_map').append('svg').attr('width', w).attr('height', h)
  Promise.all([
    d3.json('./data/kenya.json'),
    d3.csv('./data/Financing percentage.csv', function (d) {
      data.set(+d.id, +d.rate)
    })
  ]).then(function (loadData) {
    let topo = loadData[0]
    // create a tooltip
    const Tooltip = d3
      .select('#kenya_map')
      .append('div')
      .style('opacity', 0)
      .attr('class', 'tooltip')
      .style('background-color', 'white')
      .style('border', 'solid')
      .style('border-width', '2px')
      .style('border-radius', '5px')
      .style('padding', '5px')
      .style('width', 'auto')

    let mouseOver = (event) => {
      d3.selectAll('.Country').transition().duration(200).style('opacity', 0.5)
      d3.select(event.target)
        .transition()
        .duration(200)
        .style('opacity', 1)
        .style('stroke', 'black')

      Tooltip.style('opacity', 1)
      d3.select(event.target).style('stroke', 'black').style('opacity', 1)
    }

    let mouseLeave = (event) => {
      Tooltip.style('opacity', 0)
      d3.select(event.target).style('stroke', 'none').style('opacity', 0.8)

      d3.selectAll('.Country').transition().duration(200).style('opacity', 1)
      d3.select(event.target).transition().duration(200).style('stroke', 'transparent')
    }

    let mouseMove = (event, d) => {
      if (d.properties?.COUNTY_NAM) {
        Tooltip.html(`${d.properties?.COUNTY_NAM}: ${d.total.toFixed(2)} %`)
          .style('left', event.pageX + 'px')
          .style('top', event.pageY + 'px')
      }
    }

    if (title) {
      svgMap.append('text').attr('class', 'title').attr('y', 24).html(title)
    }

    if (subtitle) {
      svgMap.append('text').attr('class', 'subTitle').attr('y', 55).html(subtitle)
    }

    // Draw the map
    svgMap
      .append('g')
      .selectAll('path')
      .data(topo.features)
      .join('path')
      // draw each country
      .attr('d', path.projection(projection))
      // set the color of each country
      .attr('fill', function (d) {
        d.total = data.get(d.properties?.COUNTY_COD)
        return colorScale(d.total)
      })
      .style('stroke', 'transparent')
      .attr('class', function () {
        return 'Country'
      })
      .style('opacity', 1)
      .on('mouseover', mouseOver)
      .on('mousemove', mouseMove)
      .on('mouseleave', mouseLeave)
  })
}

onMounted(async () => {
  await drawMap()
})
</script>

<style>
.tooltip {
  position: absolute;
  text-align: center;
  width: 60px;
  height: 28px;
  padding: 2px;
  font: 12px sans-serif;
  background: lightsteelblue;
  border: 0px;
  border-radius: 8px;
  pointer-events: none;
  z-index: 999;
}
</style>
