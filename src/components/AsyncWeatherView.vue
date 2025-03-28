<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner -->
        <div class="text-white p-4 bg-weather-secondary w-full text-center" v-if="route.query.preview">
            <p>
                You are currently previewing this city, click the "+" icon to start tracking this city.
            </p>
        </div>

        <!-- Weather Overview -->
        <div class="flex flex-col items-center text-white pt-8 pb-6">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="text-sm mb-6">
                {{
                    new Date(weatherData.current.time).toLocaleDateString(
                        "en-us",
                        {
                            weekday: "short",
                            day: "2-digit",
                            month: "long",
                        }
                    )
                }}
                {{
                    new Date(weatherData.current.time).toLocaleTimeString(
                        "en-us",
                        {
                            timeStyle: "short",
                        }
                    )
                }}
            </p>
            <p class="text-8xl mb-6">
                {{ Math.round(weatherData.current.temperature_2m) }}&deg;
            </p>
            <p>
                During the
                {{ getDayOrNight(weatherData.current.is_day) }}
            </p>
            <p class="capitalize">
                {{ getDescription(weatherData.current.weather_code, weatherData.current.is_day) }}
            </p>
            <img :src="getImageURL(weatherData.current.weather_code, weatherData.current.is_day)"
                class="w-[150px] h-auto">
        </div>

        <!-- Hourly Weather -->
        <div class="max-w-screen-md w-full pb-6">
            <div class="mx-8 text-white">
                <h2 class="mb-4">Hourly Weather</h2>
                <div class="flex gap-10 overflow-x-scroll">
                    <div class="flex flex-col gap-4 items-center" v-for="n in 24" :key="n">
                        <p class="whitespace-nowrap text-md">
                            {{
                                new Date(
                                    weatherData.hourly.time[n - 1]
                                ).toLocaleTimeString("en-us", {
                                    hour: "numeric",
                                })
                            }}
                        </p>
                        <img :src="getImageURL(weatherData.hourly.weather_code[n - 1], weatherData.hourly.is_day[n - 1])"
                            class="w-auto h-[50px] object-cover">
                        <p class="text-xl">
                            {{ Math.round(weatherData.hourly.temperature_2m[n - 1]) }}&deg;
                        </p>
                    </div>
                </div>
            </div>
        </div>

        <hr class="border-white border-opacity-10 border w-full" />

        <!-- Weekly Weather -->
        <div class="max-w-screen-md w-full pt-8 pb-6">
            <div class="mx-8 text-white">
                <h2 class="mb-4">7 Day Forecast</h2>
                <div v-for="n in 7" :key="n" class="flex items-center">
                    <p class="flex-1">
                        {{
                            new Date(weatherData.daily.time[n]).toLocaleDateString(
                                "en-us",
                                {
                                    weekday: "long",
                                }
                            )
                        }}
                    </p>
                    <img class="w-[50px] h-[50px] object-cover"
                        :src="getImageURL(weatherData.daily.weather_code[n], 'day')" />
                    <div class="flex gap-2 flex-1 justify-end">
                        <p>H: {{ Math.round(weatherData.daily.temperature_2m_max[n]) }}</p>
                        <p>L: {{ Math.round(weatherData.daily.temperature_2m_min[n]) }}</p>
                    </div>
                </div>
            </div>
        </div>

        <div class="flex items-center gap-2 py-6 text-white cursor-pointer duration-150 hover:text-red-500"
            @click="removeCity" v-if="!route.query.preview">
            <i class="fa-solid fa-trash"></i>
            <p>Remove City</p>
        </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';
import descriptions from '@/assets/descriptions';

const route = useRoute();
const getWeatherData = async () => {
    await new Promise((res) => setTimeout(res, 200));
    try {
        const weatherData = await axios.get(`https://api.open-meteo.com/v1/forecast?latitude=${route.query.lat}&longitude=${route.query.lng}&daily=weather_code,temperature_2m_max,temperature_2m_min&hourly=weather_code,temperature_2m,is_day&current=temperature_2m,weather_code,is_day&timezone=Asia%2FShanghai&forecast_days=8`);
        return weatherData.data;
    } catch (e) {
        console.log(e);
    }
};
const getDayOrNight = (is_day) => {
    return is_day ? 'day' : 'night';
};
const getDescription = (weather_code, is_day) => {
    const dayOrNight = getDayOrNight(is_day);
    return descriptions[weather_code][dayOrNight].description;
};
const getImageURL = (weather_code, is_day) => {
    const dayOrNight = getDayOrNight(is_day);
    return descriptions[weather_code][dayOrNight].image;
};
const weatherData = await getWeatherData();
const router = useRouter();
const removeCity = () => {
    if (!localStorage.getItem('savedCities')) return;
    const savedCities = JSON.parse(localStorage.getItem('savedCities'));
    const updatedSavedCities = savedCities.filter((savedCity) => savedCity.id !== route.query.id);
    localStorage.setItem('savedCities', JSON.stringify(updatedSavedCities));
    router.push({
        name: 'home',
    })
}
</script>