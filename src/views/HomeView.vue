<script>
import axios from "axios";
import SimpleInput from "./../components/simpleInput.vue";

export default {
  name: "WeatherHome",
  components: { SimpleInput },
  data() {
    return {
      preferences: { language: "", metricUnit: "" },
      bruteWeatherData: {
        coord: {
          lon: 10.99,
          lat: 44.34,
        },
        weather: [
          {
            id: 501,
            main: "Rain",
            description: "moderate rain",
            icon: "10d",
          },
        ],
        base: "stations",
        main: {
          temp: 298.48,
          feels_like: 298.74,
          temp_min: 297.56,
          temp_max: 300.05,
          pressure: 1015,
          humidity: 64,
          sea_level: 1015,
          grnd_level: 933,
        },
        visibility: 10000,
        wind: {
          speed: 0.62,
          deg: 349,
          gust: 1.18,
        },
        rain: {
          "1h": 3.16,
        },
        clouds: {
          all: 100,
        },
        dt: 1661870592,
        sys: {
          type: 2,
          id: 2075663,
          country: "IT",
          sunrise: 1661834187,
          sunset: 1661882248,
        },
        timezone: 7200,
        id: 3163858,
        name: "Zocca",
        cod: 200,
      },
      inputs: {
        city: "",
        cityValid: true,
        stateCode: "",
        countryCode: "",
      },
      geolocation: {
        lat: undefined,
        lon: undefined,
        result: undefined,
        city: "",
        state: "",
        fullName: "",
      },
      geoCity: "",
      geocityLocation: "",
      editCity: false,
      // eslint-disable-next-line no-control-regex
      regex: new RegExp("^[0-9a-zA-Z\b]+$"),
    };
  },
  created() {
    /* -----  Change Page Title -----*/
    document.title = "Previsão do Tempo";
    /* -----  Get Location by navigator -----*/
    this.getGeolocatization();
    /* -----  get last Weather Forecast request -----*/
    if (localStorage.getItem("weatherForecast")) {
      this.bruteWeatherData = JSON.parse(
        localStorage.getItem("weatherForecast")
      );
    }
  },
  methods: {
    getGeolocatization() {
      let successCallback = (result) => {
        this.geolocation.lat = result.coords.latitude;
        this.geolocation.lon = result.coords.longitude;
        this.geolocation.result = true;
      };
      let errorCallback = (result) => {
        console.log(result.message);
      };
      navigator.geolocation.getCurrentPosition(successCallback, errorCallback);
    },
    getWeather() {
      if (!this.geolocation.result) {
        console.log("não concedido");
        /*Adicionar Alerta via component general*/
        return;
      }
      axios
        .get(
          "https://api.openweathermap.org/data/2.5/weather?lat=" +
            this.geolocation.lat +
            "&lon=" +
            this.geolocation.lon +
            "&appid=" +
            import.meta.env.VITE_API_KEY
        )
        .then((response) => {
          console.log(response.data);
          this.bruteWeatherData = response.data;
          if (response.data.cod == 200) {
            localStorage.setItem(
              "weatherForecast",
              JSON.stringify(this.bruteWeatherData)
            );
          }
        });
    },
    getWeatherByCity() {
      if (!this.inputs.city == "" || undefined) {
        axios
          .get(
            "https://api.openweathermap.org/data/2.5/weather?q=" +
              this.inputs.city +
              "&appid=" +
              import.meta.env.VITE_API_KEY
          )
          .then((response) => {
            console.log(response.data);
            this.bruteWeatherData = response.data;
            console.log(this.bruteWeatherData);
            this.editCity = false;
          });
      }
    },
  },
};
</script>
<template>
  <div class="mainBody">
    <main class="mainContainer" v-if="true">
      <div class="navbar">
        <nav>
          <RouterLink to="/">Home</RouterLink>
          <RouterLink to="/about">About</RouterLink>
        </nav>
        <select name="" id="" disabled="disabled" placeholder="Estado">
          <option value="SP">Sampa</option>
          <option value="RJ">Rio de Fevereiro</option>
        </select>
        <input type="text" placeholder="Cidade..." pattern="^[a-zA-Z0-9]+$" />
      </div>
      <div class="updatedIn" @click="getWeather()">
        Atualizado em 0m <img src="../assets/icons/update.svg" height="12" />
      </div>
      <div class="weatherIcon">
        <img
          :src="
            'http://openweathermap.org/img/wn/' +
            this.bruteWeatherData.weather[0].icon +
            '@2x.png'
          "
          :alt="this.bruteWeatherData.weather[0].description"
        />
      </div>
      <div class="weatherCity">
        <template v-if="this.editCity == false">
          <span>{{
            this.bruteWeatherData.name +
            ", " +
            this.bruteWeatherData.sys.country +
            " "
          }}</span>
          <img
            @click="this.editCity = !this.editCity"
            src="../assets/icons/edit.svg"
          />
        </template>
        <template v-else>
          <SimpleInput
            placeholder="Cidade"
            v-model="this.inputs.city"
          ></SimpleInput>
          {{ " " }}
          <img
            @click="this.editCity = !this.editCity"
            src="../assets/icons/procurar.svg"
          />
        </template>
      </div>
      <div class="mainTemperature">
        <div class="averageTemp">
          {{ (this.bruteWeatherData.main.temp - 273.15).toFixed() }} ºC
        </div>
        <div class="maxMinTemp">
          <div class="maxTemp">
            <i>↑</i>
            <p>
              {{ (this.bruteWeatherData.main.temp_max - 273.15).toFixed() }}º
            </p>
          </div>
          <div class="minTemp">
            <i>↓</i>
            <p>
              {{ (this.bruteWeatherData.main.temp_min - 273.15).toFixed() }}º
            </p>
          </div>
        </div>
      </div>
      <div class="tempTex">
        {{ this.bruteWeatherData.weather[0].description }}
      </div>
      <div class="cardsContainer">
        <article class="card grid2">
          <h6>Sensaçào Térmica</h6>
          <p>
            {{ (this.bruteWeatherData.main.feels_like - 273.15).toFixed() }}
          </p>
        </article>
        <article class="card">
          <h6>Visibilidade</h6>
          <p>{{ this.bruteWeatherData.visibility / 100 }} %</p>
        </article>
        <article class="card">
          <h6>Humidade</h6>
          <p>{{ this.bruteWeatherData.main.humidity }} %</p>
        </article>
        <article class="card">
          <h6>Vento</h6>
          <p>{{ this.bruteWeatherData.wind.speed }} m/s</p>
        </article>
      </div>
    </main>
    <main v-else>Ops! alguem deu errado...</main>
    <footer>Footer</footer>
  </div>
</template>
<style>
/*----- NAVBAR -----*/
.navbar {
  height: 56px;
  background: rgba(0, 128, 0, 0.13);
}
/*----- Main -----*/

.mainBody {
  background: linear-gradient(
    0deg,
    rgba(2, 0, 36, 1) 0%,
    rgba(9, 9, 121, 1) 35%,
    rgba(0, 212, 255, 1) 100%
  );
  min-height: 120vh;
}
.mainContainer {
  margin: 0 48px;
  text-align: center;
}
.mainContainer > div {
  background: rgba(255, 255, 255, 0.521);
  border-radius: 8px;
}
.updatedIn {
  text-align: end;
  margin: 24px 0 -12px 0;
  cursor: pointer;
}
.updatedIn:hover img {
  animation: boom 1s;
}
.weatherIcon img {
  height: 200px;
  filter: drop-shadow(0 0 4px rgba(0, 0, 0, 0.15));
}
.weatherCity {
  font-size: 32px;
  color: #484848;
  height: max-content;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}
.weatherCity img {
  height: 26px;
  cursor: pointer;
}
.weatherCity input[type="text"] {
  height: 32px;
  border: none;
  border-bottom: 2px solid #a6a6a6;
  padding: 0 4px;
  font-size: 18px;
  font-family: Inter;
  background: transparent;
}
.mainTemperature {
  display: flex;
  justify-content: center;
  gap: 12px;
  margin-top: 28px;
}
.mainTemperature .averageTemp {
  font-size: 36px;
  color: #242424;
}
.mainTemperature .maxMinTemp :is(.maxTemp, .minTemp) {
  display: flex;
}
.mainTemperature :is(.maxMinTemp, .maxTemp) i {
  font-style: normal;
}
.mainTemperature .maxMinTemp .maxTemp i {
  color: #ec6e4c;
}
.mainTemperature .maxMinTemp .minTemp i {
  color: #00bdf9;
}
.tempTex {
  font-size: 32px;
  color: #242424;
  margin: 18px 0 28px 0;
  text-transform: capitalize;
}
.cardsContainer {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(96px, 1fr));
  gap: 12px;
}
.card {
  background: white;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.25);
  height: 96px;
  padding: 8px;
}
.card h6 {
  font-weight: 400;
  font-size: 14px;
  color: #363636;
}
.card p {
  font-weight: 500;
  font-size: 20;
  color: #444444;
  line-height: 400%;
}
.grid2 {
  grid-column: 1/3;
}

@keyframes boom {
  0% {
    filter: drop-shadow(0 0 0 rgba(0, 0, 0, 1));
  }
  100% {
    filter: drop-shadow(0 0 20px rgba(0, 0, 0, 0));
    transform: rotate(360deg);
  }
}

@media (min-width: 1239px) {
  .mainContainer {
    margin: 0 200px;
    min-height: 97.9vh;
  }
  /*    <div class="loading"><span class="point point1"></span><span class="point point2"></span></div>  */
  .loading {
    aspect-ratio: 5/3;
    height: 48px;
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
  }
  .point {
    aspect-ratio: 1/1;
    height: 12px;
    background: rgb(255, 91, 27);
    border-radius: 50%;
  }
  .point1 {
    animation: up 1s ease-in-out infinite alternate;
  }
  .point2 {
    animation: up 1s 0.4s ease-in-out infinite alternate;
  }
  .point3 {
    animation: up 1s 0.8s ease-in-out infinite alternate;
  }
  @keyframes up {
    100% {
      transform: translateY(-36px);
    }
  }
}
</style>
