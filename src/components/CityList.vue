<template>
    <div v-for="savedCity in savedCities" :key="savedCity.id">
        <CityCard :savedCity="savedCity" @click="goToCityView(savedCity)" />
    </div>
    <p class="absolute top-[50vh] left-[50vw] translate-x-[-50%] translate-y-[-50%] text-white text-xl"
        v-if="savedCities.length === 0">
        No locations added. To start tracking a location, search in the field above.
    </p>
</template>

<script setup>
import axios from 'axios';
import { ref } from "vue";
import CityCard from "./CityCard.vue";
import { useRouter } from 'vue-router';

const savedCities = ref([]);
const getWeatherDataOfCities = async () => {
    if (!localStorage.getItem('savedCities')) return;
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'));

    const requests = ref([]);
    const requestError = ref(null);
    savedCities.value.forEach((city) => {
        requests.value.push(
            axios.get(`https://api.open-meteo.com/v1/forecast?latitude=${city.coords.lat}&longitude=${city.coords.lng}&daily=temperature_2m_min,temperature_2m_max&current=temperature_2m&forecast_days=1`)
        )
    })
    try {
        const weatherData = await Promise.all(requests.value);
        weatherData.forEach((value, index) => {
            savedCities.value[index].weatherData = value.data;
        })
    } catch (e) {
        requestError.value = e;
        console.log(e);
    }
    await new Promise((res) => setTimeout(res, 200));
};
await getWeatherDataOfCities();

const router = useRouter();
const goToCityView = (savedCity) => {
    router.push({
        name: 'weather',
        params: { city: savedCity.city, state: savedCity.state },
        query: {
            id: savedCity.id,
            lng: savedCity.coords.lng,
            lat: savedCity.coords.lat,
        },
    })
}
</script>
