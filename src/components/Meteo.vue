<template>
  <div class="container">
    <h1 class="my-4">App météo avec Vue.js</h1>

    <div v-if="gettingLocation">
      <i>Recherche de votre localisation en cours...</i>
    </div>

    <div class="form-groupe mb-5">
      <label for="position">Entrez le nom d'une ville</label>
      <input
        type="text"
        id="position"
        class="form-control"
        v-model="requete"
        @keypress.enter="goMeteo"
      />
    </div>

    <div v-if="temps" class="w-75 m-auto">
      <h3 class="text-center mb-3">Position: {{ temps.name }}</h3>
      <div class="card text-center p-5">
        <p class="text-affichage">
          Température : {{ temps.main.temp.toFixed() }}°
        </p>
        <p class="text-affichage">
          Ressentie : {{ temps.main.feels_like.toFixed() }}°
        </p>
        <p class="text-affichage">Temps : {{ temps.weather[0].description }}</p>
      </div>
    </div>

    <div v-if="errorStr">Désolé, une erreur est survenue: {{ errorStr }}</div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "Meteo",
  data() {
    return {
      location: null,
      gettingLocation: false,
      errorStr: null,

      requete: "",
      temps: undefined,
      api_code: "7d5abfca3e77fe49c9163b49ac69dc07",
      url_recherche: "https://api.openweathermap.org/data/2.5/weather?",
    };
  },
  methods: {
    goMeteo() {

      const mySearch = this.requete
        ? `q=${this.requete}`
        : `lat=${this.location.coords.latitude}&lon=${this.location.coords.longitude}`;

      axios
        .get(
          `${this.url_recherche}${mySearch}&units=metric&APPID=${this.api_code}&lang=fr`
        )
        .then((response) => {
          this.temps = response.data;
          console.log(this.temps)
        });
      this.requete = "";
    },
  },
  created() {
    //do we support geolocation
    if (!("geolocation" in navigator)) {
      this.errorStr = "Localisation non disponible.";
      return;
    }

    this.gettingLocation = true;
    // get position
    navigator.geolocation.getCurrentPosition(
      (pos) => {
        this.gettingLocation = false;
        this.location = pos;
        this.goMeteo();
      },
      (err) => {
        this.gettingLocation = false;
        this.errorStr = err.message;
      }
    );
  },
};
</script>
<style >
body {
  color: #3a3a3a !important;
}
.text-affichage {
  font-size: 30px;
  font-weight: 300;
  line-height: 1.2;
}
</style>