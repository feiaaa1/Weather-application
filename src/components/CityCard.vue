<!--
 * @Author: wx 2504597640@qq.com
 * @Date: 2024-10-14 20:32:55
 * @LastEditors: wx 2504597640@qq.com
 * @LastEditTime: 2024-10-20 22:40:18
 * @FilePath: \net_ninja_vue_3_weather_app-main\src\components\CityCard.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div
    class="card flex py-6 px-3 rounded-md bg-transparent cursor-pointer shadow-md transition-all duration-300  hover:shadow-lg hover:-translate-y-[2px] ease-out"
  >
    <div class="flex flex-col flex-1">
      <h2 class="text-3xl">{{ city.city }}</h2>
      <h3>{{ city.state }}</h3>
    </div>

    <div class="flex flex-col gap-2">
      <p class="text-3xl self-end">
        {{ Math.round(city.weather.main.temp) }}&deg;
      </p>
      <div class="flex gap-2">
        <span class="text-xs">
          H:
          {{ Math.round(city.weather.main.temp_max) }}&deg;
        </span>
        <span class="text-xs">
          L:
          {{ Math.round(city.weather.main.temp_min) }}&deg;
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  city: {
    type: Object,
    default: () => {},
  },
});
</script>
