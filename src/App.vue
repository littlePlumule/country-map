<template>
  <div class="map" id="map"></div>
</template>

<script setup>
import L from "leaflet";
import { ref, onMounted } from "vue";

const { VITE_COUTRIES_API } = import.meta.env;

const map = ref({});
const countries = ref(null);

function initMap() {
  map.value = L.map("map", {
    center: [23.5, 121],
    zoom: 5,
  });

  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution:
      '&copy; <a target="_blank" href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
    maxZoom: 6,
    minZoom: 3,
  }).addTo(map.value);
}

function setMarker() {
  for (let country of countries.value) {
    let info = `
      <div class="title">
        <img class="image" src="${country.flags.svg}"> 
        <h2 class="name">${country.name.common}</h2>
      </div>
      <div>Capital：${country.capital ? country.capital[0] : "unknown"}</div>
      <div>
        Currency Symbol：${
          country.currencies
            ? Object.values(country.currencies)[0].symbol
            : "unknown"
        }
      </div>
    `;
    L.marker(country.latlng).addTo(map.value).bindPopup(info);
  }
}

function getCountries() {
  fetch(VITE_COUTRIES_API)
    .then((res) => res.json())
    .then((json) => {
      countries.value = json;
      setMarker();
    })
    .catch(() => {
      alert("系統異常，請稍後再試");
    });
}

onMounted(() => {
  initMap();
  getCountries();
});
</script>

<style>
.map {
  width: 100%;
  height: 100%;
}

.title {
  height: 20px;
  display: flex;
  align-items: center;
  margin-bottom: 5px;
}

.name {
  font-size: 15px;
}

.image {
  vertical-align: middle;
  height: 100%;
  margin-right: 5px;
}
</style>
