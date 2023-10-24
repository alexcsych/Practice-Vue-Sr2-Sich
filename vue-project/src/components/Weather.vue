<template>
  <div class="p-4">
    <div class="mb-4">
      <p class="text-lg font-semibold">City</p>
      <input v-model="newCity" class="border p-2" type="text" />
      <button
        @click="addCity"
        class="bg-blue-500 text-white px-4 py-2 rounded-md ml-2"
      >
        Add new City
      </button>
    </div>
    <div class="mb-4">
      <p class="text-lg font-semibold">Weather</p>
      <select v-model="selectedCity" name="weather" class="border p-2">
        <option v-for="(city, index) in cities" :key="index" :value="city">
          {{ city }}
        </option>
      </select>
      <router-link
        v-show="selectedCity !== ''"
        :to="'/weather-info/' + selectedCity"
        class="ml-2"
      >
        <button class="bg-blue-500 text-white px-4 py-2 rounded-md">
          Get Weather
        </button>
      </router-link>
    </div>
    <div>
      <div v-if="loading" class="text-blue-500 font-semibold">Loading...</div>
      <div v-else>
        <div v-if="error" class="text-red-500 font-semibold">{{ error }}</div>
        <div v-else>
          <table
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
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref, onMounted, onUnmounted } from 'vue'

const cities = ref([])
const newCity = ref('')
const selectedCity = ref('')
const loading = ref(true)
const error = ref('')
const weather = ref({})
const apiKey = '7914d5a440960cfd5df3bd0388a7ad0f'

const addCity = () => {
  if (newCity.value && !cities.value.includes(newCity.value)) {
    cities.value.push(newCity.value)
    newCity.value = ''
  }
}

const convertedTemperature = temp => {
  return Math.round(temp - 273.15)
}

const geolocation = async pos => {
  const crd = pos.coords

  await axios
    .get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${crd.latitude}&lon=${crd.longitude}&appid=${apiKey}`
    )
    .then(({ data }) => {
      weather.value = data
      if (cities.value.length === 0) {
        cities.value.push(data.name)
      }
    })
    .catch(() => {
      error.value = 'Weather data not found for this city.'
    })
    .finally(() => {
      loading.value = false
    })
}

onMounted(() => {
  if (
    localStorage.getItem('cities') !== null &&
    localStorage.getItem('cities') !== ''
  ) {
    cities.value = JSON.parse(localStorage.getItem('cities'))
  }
  navigator.geolocation.getCurrentPosition(geolocation)
})

onUnmounted(() => {
  localStorage.setItem('cities', JSON.stringify(cities.value))
})
</script>
