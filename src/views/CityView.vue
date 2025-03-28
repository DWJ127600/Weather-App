<template>
    <div>
        <Suspense>
            <AsyncWeatherView />
            <template #fallback>
                <CityViewSkeleton />
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import AsyncWeatherView from '@/components/AsyncWeatherView.vue';
import CityViewSkeleton from '@/components/CityViewSkeleton.vue';
</script>