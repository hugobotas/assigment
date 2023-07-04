<script lang="ts">
import { GChart } from 'vue-google-charts'
import { defineComponent, ref } from 'vue'

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

export default defineComponent({
  components: {
    GChart
  },
  setup() {
    const lisbon = ref<[string, IStationEntry][]>([])
    const lisbon_graph = ref<[string, number | string][]>([])
    return {
      lisbon,
      lisbon_graph
    }
  },
  data() {
    return {
      chartOptions: {
        title: '24h Temperature Variation in Lisbon',
        width: 700,
        height: 500,
        vAxis: {
          title: 'Temperature (ºC)'
        }
      }
    }
  },
  methods: {
    async fetchData() {
      const response = await fetch(
        'https://api.ipma.pt/open-data/observation/meteorology/stations/observations.json'
      )
      const data: IStationEntries = await response.json()

      for (const hour in data) {
        this.lisbon = [...this.lisbon, [hour, data[hour]['1200579']]]
        this.lisbon_graph = [...this.lisbon_graph, [hour, data[hour]['1200579'].temperatura]]
      }
      this.lisbon.sort()
      this.lisbon_graph.sort()
      this.lisbon_graph = [['Time', 'Temperature'], ...this.lisbon_graph]
    }
  }
})
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
