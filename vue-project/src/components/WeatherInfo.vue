<template>
  <div>
    <div v-if="loading" class="loading">Loading...</div>
    <div v-else>
      <div v-if="error" class="error">{{ error }}</div>
      <div v-else>
        <table>
          <tr>
            <td>{{ weather.sys.country }}</td>
            <td>{{ weather.name }}</td>
            <td>[{{ weather.coord.lat }}°, {{ weather.coord.lon }}°]</td>
          </tr>
          <tr>
            <td>humidity</td>
            <td>{{ weather.main.humidity }}</td>
            <td class="empty"></td>
          </tr>
          <tr>
            <td>temp/AVG</td>
            <td>{{ convertedTemperature(weather.main.temp) }}°C</td>
            <td>{{ weather.main.temp }} K</td>
          </tr>
          <tr>
            <td>main</td>
            <td>{{ weather.weather[0].main }}</td>
            <td class="empty"></td>
          </tr>
          <tr>
            <td>pressure</td>
            <td>{{ weather.main.pressure }} hpa</td>
            <td class="empty"></td>
          </tr>
          <tr>
            <td>description</td>
            <td>{{ weather.weather[0].description }}</td>
            <td class="empty"></td>
          </tr>
          <tr>
            <td>wind</td>
            <td>{{ weather.wind.speed }}</td>
            <td class="empty"></td>
          </tr>
          <tr>
            <td>deg</td>
            <td>{{ weather.wind.deg }}</td>
            <td class="empty"></td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref, onMounted, toRefs } from 'vue'

const props = defineProps({
  city: String
})
const { city } = toRefs(props)
const loading = ref(true)
const error = ref('')
const weather = ref({})
const apiKey = '7914d5a440960cfd5df3bd0388a7ad0f'

const convertedTemperature = temp => {
  return Math.round(temp - 273.15)
}

onMounted(() => {
  axios
    .get(
      `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&appid=${apiKey}`
    )
    .then(({ data }) => {
      weather.value = data
    })
    .catch(() => {
      error.value = 'Weather data not found for this city.'
    })
    .finally(() => {
      loading.value = false
    })
})
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  border: 1px solid #ccc;
  padding: 8px;
}

th {
  background-color: #f2f2f2;
}

td {
  text-align: center;
}

.loading {
  font-size: 1.5rem;
  font-weight: bold;
  color: #333;
}

.error {
  font-size: 1.5rem;
  color: red;
}

.empty {
  background-color: #ccc;
}
</style>
