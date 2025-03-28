<template>
  <main class="container text-white">
    <div class="pt-4 mb-4">
      <input type="text" placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
        v-model="searchQuery" @input="getSearchResults">
      <ul class="bg-weather-secondary text-white w-full shadow-md py-2 px-1 mt-1">
        <p v-if="searchError">Sorry, something went wrong, please try again.</p>
        <p v-else-if="!searchError && mapboxSearchResults.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li class="py-2 cursor-pointer" v-for="searchResult in mapboxSearchResults" :key="searchResult.id"
            @click="previewWeather(searchResult)">
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';
import CityList from '@/components/CityList.vue';
import CityCardSkeleton from '@/components/CityCardSkeleton.vue';

let queryTimeout = null;
const mapboxAPIKey = 'pk.eyJ1IjoiZHJmdHl1aW9wd3d3IiwiYSI6ImNtMmJqNXE1ZzBzYWYya29tZTFhZ21oeDUifQ.I2QHtymCKR-bGqmndCOk5Q';
const searchQuery = ref('');
const mapboxSearchResults = ref([]);
const searchError = ref(null);
const router = useRouter();

const getSearchResults = () => {
  clearTimeout(queryTimeout);
  queryTimeout = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
        mapboxSearchResults.value = result.data.features;
      } catch {
        searchError.value = true;
      }
      return;
    }
    mapboxSearchResults.value = [];
  }, 300)
}

const previewWeather = (searchResult) => {
  const [city, state] = searchResult.place_name.split(',');
  const routeObj = {
    name: 'weather',
    params: { city, state: state.replaceAll(' ', '') },
    query: {
      lng: searchResult.geometry.coordinates[0],
      lat: searchResult.geometry.coordinates[1],
      preview: true,
    },
  };
  if (localStorage.getItem('savedCities')) {
    const savedCities = JSON.parse(localStorage.getItem('savedCities'));
    savedCities.forEach((savedCity) => {
      if (savedCity.coords.lng == routeObj.query.lng && savedCity.coords.lat == routeObj.query.lat) {
        delete routeObj.query.preview;
      }
    })
  }
  router.push(routeObj);
}
</script>
