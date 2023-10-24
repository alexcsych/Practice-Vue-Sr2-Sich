<template>
  <div>
    <div>
      <p>City</p>
      <input type="text" v-model="newCity" />
      <button @click="addCity">Add new City</button>
    </div>
    <div>
      <p>Weather</p>
      <select name="weather" v-model="selectedCity">
        <option v-for="(city, index) in cities" :key="index" :value="city">
          {{ city }}
        </option>
      </select>
      <router-link
        v-show="selectedCity !== ''"
        v-bind:to="'/weather-info/' + selectedCity"
      >
        <button>Get Weather</button>
      </router-link>
    </div>
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

<style scoped>
div {
  margin: 10px;
}

p {
  font-size: 16px;
  font-weight: bold;
}

input,
select {
  padding: 5px;
  margin: 5px;
}

button {
  background-color: #007bff;
  color: white;
  padding: 5px 10px;
  border: none;
  cursor: pointer;
}
</style>
