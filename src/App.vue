<template>
  <div class="container">
    <div class="sidebar">
      <img alt="Vue logo" src="./assets/weather.png" />
      <form action="search-location" @submit.prevent="getWeather">
        <input
          type="text"
          placeholder="indiquez votre ville"
          v-model="citySearch"
          autocomplete="off"
        />
      </form>
    </div>
    <header class="localisation-info" v-if="searchResult">
      <h1>{{ weather.cityName }}</h1>
      <p>{{ weather.country }}</p>
    </header>
    <section class="details" v-if="searchResult" :class="isDay ? 'day':'night'">
      <h2>{{ weather.temperature }}°C</h2>
      <p>{{ weather.description }}</p>
      <p>{{ weather.lowTemp }}</p>
      <p>{{ weather.highTemp }}</p>
      <p>{{ weather.humidity }}</p>
    </section>
    <div class="not-found" v-if="!searchResult">
      <h3>City Not Found</h3>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      isDay: true,
      searchResult: false,
      citySearch: "",
      weather: {
        cityName: "Bordeaux",
        country: "FR",
        temperature: 9,
        description: "Cloudy with a chance of meatballs",
        lowTemp: 0,
        highTemp: 15,
        humidity: 55,
      },
    };
  },
  methods: {
    getWeather: async function() {
      console.log(this.citySearch);
      const key = "ec80fe5c759a7923eb1c40f97cf4d4bf";
      const callURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;

      //? APPEL A l'API avec un await dans un try
      try {
        const response = await fetch(callURL);
        const data = await response.json();
        console.log(data);

        this.citySearch = "";
        this.weather.cityName = data.name;
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_Like);
        this.weather.humidity = Math.round(data.main.humidity);

        this.searchResult = true;

        //? Check if it's daytime or nighttime

        const dayNight = data.weather[0].icon;

        if (dayNight.includes("d")) {
          this.isDay = true;
        } else {
          this.isDay = false;
        }
      } catch (error) {
        console.log(error);
        this.searchResult = false;
      }
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  list-style: none;
  text-decoration: none;
}

template {
  height: 100vh;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.container {
  display: flex;
  border: 1px solid rgb(121, 128, 243);
  background-color: mistyrose;
}

img {
  width: 200px;
  margin-top: 10px;
}

.sidebar {
  height: 30vh;
  position: relative;
}

input {
  position: absolute;
  top: 225px;
  right: 10px;
}

.details,
.localisation-info {
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin-left: 30px;
}
</style>
