<!--
 * @Author: wx 2504597640@qq.com
 * @Date: 2024-10-14 20:32:55
 * @LastEditors: wx 2504597640@qq.com
 * @LastEditTime: 2024-10-20 13:56:47
 * @FilePath: \net_ninja_vue_3_weather_app-main\src\App.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div
  id="bg"
    class="flex flex-col min-h-screen font-Roboto "
  >
    <SiteNavigation />
    <RouterView class="flex-1" v-slot="{ Component }">
      <Transition name="page">
        <component :is="Component" />
      </Transition>
    </RouterView>
  </div>
</template>

<script setup>
import { RouterView } from "vue-router";
import SiteNavigation from "./components/SiteNavigation.vue";
</script>

<style scoped>
.page-enter-active {
  transition: 600ms ease all;
}

.page-enter-from {
  opacity: 0;
}

#bg {
  background: url("/src/assets/1.jpg");
  background-size: cover;
  background-position: center;
  transition: all .3s;
}
</style>
