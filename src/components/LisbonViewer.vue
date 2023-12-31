<script setup lang="ts">
import { GChart } from 'vue-google-charts'
import { ref } from 'vue'

interface IStationEntries {
  [key: string]: {
    [key: number]: IStationEntry
  }
}
interface IStationEntry {
  humidade: number
  idDireccVento: number
  intensidadeVento: number
  intensidadeVentoKM: number
  precAcumulada: number
  pressao: number
  radiacao: number
  temperatura: number
}
const lisbon = ref<[string, IStationEntry][]>([])
const lisbon_graph = ref<[string, number | string][]>([])
const chartOptions = {
  title: '24h Temperature Variation in Lisbon',
  width: 700,
  height: 500,
  vAxis: {
    title: 'Temperature (ºC)'
  }
}
async function fetchData() {
  const response = await fetch(
    'https://api.ipma.pt/open-data/observation/meteorology/stations/observations.json'
  )
  const data: IStationEntries = await response.json()
  for (const hour in data) {
    lisbon.value = [...lisbon.value, [hour, data[hour]['1200579']]]
    lisbon_graph.value = [...lisbon_graph.value, [hour, data[hour]['1200579'].temperatura]]
  }
  lisbon.value.sort()
  lisbon_graph.value.sort()
  lisbon_graph.value = [['Time', 'Temperature'], ...lisbon_graph.value]
}
</script>
<template>
  <div class="box">
    <button type="button" @click="fetchData">Get Data</button>
  </div>
  <div v-if="lisbon.length" class="row">
    <div class="column">
      <table class="table table-striped">
        <thead>
          <tr>
            <th>Time</th>
            <th>Humidity</th>
            <th>Wind Direction</th>
            <th>Wind Intensity</th>
            <th>Wind Speed</th>
            <th>Pressure</th>
            <th>Radiation</th>
            <th>Temperature</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="hour in lisbon" v-bind:key="hour[0]">
            <td>{{ hour[0] }}</td>
            <td>{{ hour[1].humidade }}</td>
            <td>{{ hour[1].idDireccVento }}</td>
            <td>{{ hour[1].intensidadeVento }}</td>
            <td>{{ hour[1].intensidadeVentoKM }}</td>
            <td>{{ hour[1].pressao }}</td>
            <td>{{ hour[1].radiacao }}</td>
            <td>{{ hour[1].temperatura }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div v-if="lisbon.length" class="column">
      <GChart type="LineChart" :data="lisbon_graph" :options="chartOptions" />
    </div>
  </div>
</template>
