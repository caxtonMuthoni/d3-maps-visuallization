<template>
  <main class="container mx-auto">
    <div class="flex my-2 py-2 gap-4 lg:w-1/2">
      <select
        v-model="countyToAdd"
        name=""
        id=""
        class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
        @change="addCountyToCompare"
      >
        <option value="" selected disabled>Select county to add</option>
        <option v-for="county in counties" :key="county" :value="county">
          {{ county.COUNTY_NAM }}
        </option>
      </select>
    </div>
    <div class="flex gap-2 items-center" v-if="compares.length">
      <span
        v-for="compare in compares"
        :key="compare.COUNTY_NAM"
        class="inline-block bg-teal-200 text-teal-800 text-xs px-2 rounded-full uppercase font-semibold tracking-wide"
        >{{ compare.COUNTY_NAM }}</span
      >

      <button
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline disabled:bg-blue-400"
        :disabled="compares.length < 2"
        @click="compareCouties"
      >
        Compare counties
      </button>

      <button
        class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
        @click="clearCompares"
      >
        Clear counties
      </button>
    </div>
    <div class="flex flex-col w-full h-screen">
      <div class="w-52 h-52" v-if="isComparing">
        <CheMap key="fyrtuygjhvutyvg" :scale="700" />
      </div>

      <div class="w-full h-full" v-else>
        <CheMap
          key="fyrtuygjhvutyvg"
          :scale="3700"
          title="Recent OSR Trend"
          subtitle="Collected revenue, KES M"
        />
      </div>

      <div class="w-full" v-if="isComparing">
        <RacingChart></RacingChart>
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import CheMap from '../components/CheMap.vue'
import * as d3 from 'd3'
import RacingChart from '../components/RacingChart.vue'

const counties = ref([])
const countyToAdd = ref('')
const compares = ref([])
const isComparing = ref(false)

const addCountyToCompare = () => {
  if (countyToAdd.value) {
    const county = compares.value.find((cm) => cm.COUNTY_NAM == countyToAdd.value.COUNTY_NAM)
    if (county) {
      return
    }
    compares.value.push(countyToAdd.value)
  }
  countyToAdd.value = ''
}

d3.json('./data/kenya.json').then((data) => {
  data.features.forEach((d) => {
    counties.value.push(d.properties)
  })
})

const compareCouties = () => {
  isComparing.value = true
}

const clearCompares = () => {
  compares.value = []
  isComparing.value = false
}
</script>
