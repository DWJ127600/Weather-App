<template>
    <div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer">
        <div class="flex flex-col flex-1">
            <h2 class="text-3xl">{{ savedCity.city }}</h2>
            <h3>{{ savedCity.state }}</h3>
        </div>

        <div class="flex flex-col gap-2">
            <p class="text-3xl self-end">
                {{ Math.round(savedCity.weatherData.current.temperature_2m) }}&deg;
            </p>
            <div class="flex gap-2">
                <span class="text-xs">
                    H:
                    {{ Math.round(savedCity.weatherData.daily.temperature_2m_max[0]) }}&deg;
                </span>
                <span class="text-xs">
                    L:
                    {{ Math.round(savedCity.weatherData.daily.temperature_2m_min[0]) }}&deg;
                </span>
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    savedCity: {
        type: Object,
        default: () => { },
    },
});
</script>
