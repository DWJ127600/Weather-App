<template>
    <div class="flex flex-col flex-1">
        <!-- Banner -->
        <div class="text-white p-4 bg-weather-secondary w-full text-center" v-if="route.query.preview">
            <p>
                You are currently previewing this city, click the "+" icon to start tracking this city.
            </p>
        </div>
        <!-- Weather Overview --> 
        <div class="flex flex-col items-center text-white pt-8 pb-12">
            <AnimatedPlaceholder class="max-w-[180px] h-[50px] w-full mb-2" />
            <AnimatedPlaceholder class="max-w-[130px] w-full mb-6" />
            <AnimatedPlaceholder class="max-w-[140px] h-[100px] w-full mb-6" />
            <AnimatedPlaceholder class="max-w-[300px] h-[40px] w-full mb-8" />
            <AnimatedPlaceholder class="max-w-[300px] h-[75px] w-full" />
        </div>
        <!-- Hourly Weather -->
        <div class="flex flex-col pb-6 items-center">
            <AnimatedPlaceholder class="max-w-screen-md h-[100px] w-full" />
        </div>
        <hr class="border-white border-opacity-10 border w-full" />
        <!-- Weekly Weather -->
        <div class="flex flex-col items-center pt-10">
            <AnimatedPlaceholder class="max-w-screen-md h-[100px] w-full" />
        </div>
    </div>
</template>

<script setup>
import { useRoute } from "vue-router";
import AnimatedPlaceholder from "./AnimatedPlaceholder.vue";

const route = useRoute();
</script>
