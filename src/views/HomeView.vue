<!--
 * @Author: wx 2504597640@qq.com
 * @Date: 2024-10-14 20:32:55
 * @LastEditors: wx 2504597640@qq.com
 * @LastEditTime: 2024-10-20 19:37:07
 * @FilePath: \net_ninja_vue_3_weather_app-main\src\views\HomeView.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <!-- 输入框 -->
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <transition name="search">
      <!-- 输入提示 -->
      <ul
      id="search-results"
        class="relatives text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults"
      >
      
        <p class="py-2" v-if="searchError">
          Sorry, something went wrong, please try again.
        </p>

        <p
          class="py-2"
          v-if="!searchError && mapboxSearchResults.length === 0"
        >
          No results match your query, try a different term.
        </p>

        <template v-else>
          <li
            v-for="searchResult in mapboxSearchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.place_name }}
          </li>
        </template>

      </ul>
    </transition>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <transition name="city-list">
          <CityList v-if="!mapboxSearchResults"/>
        </transition>
          <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref, watch } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";
import CityList from "../components/CityList.vue";



const router = useRouter();
const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(",");
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    },
  });
};

const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        mapboxSearchResults.value = result.data.features;
      } catch {
        searchError.value = true;
      }

      return;
    }
    mapboxSearchResults.value = null;
  }, 300);
};
</script>

<style scoped>
#search-results{
  background-color: rgba(255, 237, 237, 0.5);
  transform-origin: top;
  border-radius: 5px;
}
.search-enter-from,
.search-leave-to{
  opacity: 0;
  transform: scaleY(0.2);
}
.search-enter-active,
.search-leave-active{
  transition: all .3s ease-out;
}
.city-list-enter-active,
.city-list-leave-active {
  transition: all .3s ease-out;
}
.city-list-enter-from,
.city-list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
</style>