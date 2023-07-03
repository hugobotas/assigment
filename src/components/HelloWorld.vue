<script setup>
import { ref } from 'vue'

let lisbon = ref([])
let lisbon_graph = ref([['Time', 'Temperature']])

async function fetchData() {
  const response = await fetch(
    'https://api.ipma.pt/open-data/observation/meteorology/stations/observations.json'
  )
  const data = await response.json()

  for (const hour in data) {
    lisbon.value = [
      ...lisbon.value,
      {
        ...data[hour]['1200579'],
        time: hour
      }
    ]

    lisbon_graph.value = [
      ...lisbon_graph.value,
      [
        hour, data[hour]['1200579'].temperatura
      ]
    ]
  }
  console.log(lisbon_graph);
}
</script>
<script>
import { GChart } from 'vue-google-charts'

export default {
  components: {
    GChart
  },
  data() {
    return {
      // Array will be automatically processed with visualization.arrayToDataTable function
      chartData: [
        ['Year', 'Sales'],
        ['2014', 1000],
        ['2015', 1170],
        ['2016', 660],
        ['2017', 1030]
      ],
      chartOptions: {
        chart: {
          title: 'Company Performance',
          subtitle: 'Sales, Expenses, and Profit: 2014-2017'
        }
      }
    }
  },
  methods: {}
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
          <tr v-for="hour in lisbon" v-bind:key="hour.key">
            <td>{{ hour.time }}</td>
            <td>{{ hour.humidade }}</td>
            <td>{{ hour.idDireccVento }}</td>
            <td>{{ hour.intensidadeVento }}</td>
            <td>{{ hour.intensidadeVentoKM }}</td>
            <td>{{ hour.pressao }}</td>
            <td>{{ hour.radiacao }}</td>
            <td>{{ hour.temperatura }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div v-if="lisbon.length" class="column">
      <GChart type="LineChart" :data="lisbon_graph" :options="chartOptions" />
    </div>
  </div>
</template>
