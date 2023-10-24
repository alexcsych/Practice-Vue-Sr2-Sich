<template>
  <div>
    <div v-if="loading" class="bg-blue-100 p-4 rounded-lg">Loading...</div>
    <div v-else>
      <div v-if="error" class="bg-red-100 p-4 rounded-lg">{{ error }}</div>
      <table
        v-else
        class="table-auto w-full border-collapse border border-gray-300"
      >
        <tr>
          <td class="border px-4 py-2">{{ weather.sys.country }}</td>
          <td class="border px-4 py-2">{{ weather.name }}</td>
          <td class="border px-4 py-2">
            [{{ weather.coord.lat }}°, {{ weather.coord.lon }}°]
          </td>
        </tr>
        <tr>
          <td class="border px-4 py-2">humidity</td>
          <td class="border px-4 py-2">{{ weather.main.humidity }}</td>
          <td class="border px-4 py-2 empty"></td>
        </tr>
        <tr>
          <td class="border px-4 py-2">temp/AVG</td>
          <td class="border px-4 py-2">
            {{ convertedTemperature(weather.main.temp) }}°C
          </td>
          <td class="border px-4 py-2">{{ weather.main.temp }} K</td>
        </tr>
        <tr>
          <td class="border px-4 py-2">main</td>
          <td class="border px-4 py-2">{{ weather.weather[0].main }}</td>
          <td class="border px-4 py-2 empty"></td>
        </tr>
        <tr>
          <td class="border px-4 py-2">pressure</td>
          <td class="border px-4 py-2">{{ weather.main.pressure }} hpa</td>
          <td class="border px-4 py-2 empty"></td>
        </tr>
        <tr>
          <td class="border px-4 py-2">description</td>
          <td class="border px-4 py-2">
            {{ weather.weather[0].description }}
          </td>
          <td class="border px-4 py-2 empty"></td>
        </tr>
        <tr>
          <td class="border px-4 py-2">wind</td>
          <td class="border px-4 py-2">{{ weather.wind.speed }}</td>
          <td class="border px-4 py-2 empty"></td>
        </tr>
        <tr>
          <td class="border px-4 py-2">deg</td>
          <td class="border px-4 py-2">{{ weather.wind.deg }}</td>
          <td class="border px-4 py-2 empty"></td>
        </tr>
      </table>
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

onMounted(async () => {
  await axios
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
