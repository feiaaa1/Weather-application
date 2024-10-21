<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      :class="{bgopacity: !showPreviewBanner}"
      class="text-white bg-weather-primary p-4 w-full text-center transition-all duration-100"
    >
      <p>
        You are currently previewing this city, click the "+"
        icon to start tracking this city.
      </p>
    </div>
    <!-- Weather Overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{
          new Date(weatherData.currentTime).toLocaleDateString(
            "en-us",
            {
              weekday: "short",
              day: "2-digit",
              month: "long",
            }
          )
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString(
            "en-us",
            {
              timeStyle: "short",
            }
          )
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(weatherData.current.temp) }}&deg;
      </p>
      <p>
        Feels like
        {{ Math.round(weatherData.current.feels_like) }} &deg;
      </p>
      <p class="capitalize">
        {{ weatherData.current.weather[0].description }}
      </p>
      <img
        class="w-[150px] h-auto"
        :src="
          `http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`
        "
        alt=""
      />
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Hourly Weather -->

    
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Hourly Weather</h2>

        <v-chart class="chart w-full mb-10" :option="option" autoresize />
        
        <div class="flex gap-10 overflow-x-scroll">
          <div
            v-for="hourData in weatherData.hourly"
            :key="hourData.dt"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-md">
              {{
                new Date(
                  hourData.currentTime
                ).toLocaleTimeString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="w-auto h-[50px] object-cover"
              :src="
                `http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`
              "
              alt=""
            />
            <p class="text-xl">
              {{ Math.round(hourData.temp) }}&deg;
            </p>
          </div>
        </div>
      </div>
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Weekly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">7 Day Forecast</h2>
        <div
          v-for="day in weatherData.daily"
          :key="day.dt"
          class="flex items-center"
        >
          <p class="flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString(
                "en-us",
                {
                  weekday: "long",
                }
              )
            }}
          </p>
          <img
            class="w-[50px] h-[50px] object-cover"
            :src="
              `http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`
            "
            alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>H: {{ Math.round(day.temp.max) }}</p>
            <p>L: {{ Math.round(day.temp.min) }}</p>
          </div>
        </div>
      </div>
    </div>

    <div
      class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";
import { provide, ref, onMounted, onUnmounted } from 'vue';
import { use } from 'echarts/core';
import { CanvasRenderer } from 'echarts/renderers';
import { LineChart } from 'echarts/charts';
import {
  TitleComponent,
  TooltipComponent,
  GridComponent,
  LegendComponent,
  DataZoomComponent,
} from 'echarts/components';
import VChart, { THEME_KEY } from 'vue-echarts';

use([
  CanvasRenderer,
  LineChart,
  TitleComponent,
  TooltipComponent,
  GridComponent,
  LegendComponent,
  DataZoomComponent,
]);

provide(THEME_KEY, 'dark');

const option = ref({
  backgroundColor: 'transparent',
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'cross',
      label: {
        backgroundColor: '#6a7985'
      }
    }
  },
  legend: {
    data: ['实际温度', '体感温度'],
    top: 30,
    textStyle: {
      color: '#eee',
    },
  },
  grid: {
    left: 0,
    right: 0,
    bottom: '15%',
    top: '15%',
    containLabel: true
  },
  xAxis: {
    type: 'category',
    boundaryGap: false,
    data: ['00:00', '03:00', '06:00', '09:00', '12:00', '15:00', '18:00', '21:00'],
    name: '时间',
    nameLocation: 'middle',
    nameGap: 30,
    axisLine: {
      lineStyle: {
        color: '#eee'
      }
    },
    axisLabel: {
      color: '#eee'
    },
  },
  yAxis: {
    type: 'value',
    axisLine: {
      lineStyle: {
        color: '#eee'
      }
    },
    axisLabel: {
      color: '#eee'
    },
    splitLine: {
      lineStyle: {
        color: 'rgba(255, 255, 255, 0.1)'
      }
    }
  },
  dataZoom: [
    {
      type: 'inside',
      start: 0,
      end: 100
    },
    {
      type: 'slider',
      start: 0,
      end: 100,
      bottom: '2%',
      height: 20,
      borderColor: 'transparent',
      backgroundColor: 'rgba(255, 255, 255, 0.1)',
      fillerColor: 'rgba(84, 112, 198, 0.3)',
      handleStyle: {
        color: '#5470C6',
        borderColor: '#fff',
        borderWidth: 2,
        shadowBlur: 4,
        shadowColor: 'rgba(0, 0, 0, 0.3)',
      },
      textStyle: {
        color: '#eee',
      },
      moveHandleStyle: {
        color: '#5470C6',
      },
      selectedDataBackground: {
        lineStyle: {
          color: '#5470C6',
          width: 2,
        },
        areaStyle: {
          color: '#5470C6',
          opacity: 0.2,
        },
      },
      emphasis: {
        handleStyle: {
          borderWidth: 3,
          shadowBlur: 6,
        },
      },
    }
  ],
  series: [
    {
      name: '实际温度',
      type: 'line',
      data: [15, 13, 11, 13, 22, 27, 25, 20],
      smooth: true,
      symbol: 'circle',
      symbolSize: 8,
      lineStyle: {
        color: '#5470C6',
        width: 4
      },
      itemStyle: {
        color: '#5470C6',
        borderColor: '#fff',
        borderWidth: 2
      },
      areaStyle: {
        color: {
          type: 'linear',
          x: 0,
          y: 0,
          x2: 0,
          y2: 1,
          colorStops: [{
            offset: 0, color: 'rgba(84,112,198,0.5)'
          }, {
            offset: 1, color: 'rgba(84,112,198,0.1)'
          }],
        }
      },
      emphasis: {
        focus: 'series'
      },
      animation: true,
    },
    {
      name: '体感温度',
      type: 'line',
      data: [14, 12, 10, 12, 24, 29, 26, 19],
      smooth: true,
      symbol: 'triangle',
      symbolSize: 8,
      lineStyle: {
        color: '#FF8C00',
        width: 4
      },
      itemStyle: {
        color: '#FF8C00',
        borderColor: '#fff',
        borderWidth: 2
      },
      areaStyle: {
        color: {
          type: 'linear',
          x: 0,
          y: 0,
          x2: 0,
          y2: 1,
          colorStops: [{
            offset: 0, color: 'rgba(255,140,0,0.5)'
          }, {
            offset: 1, color: 'rgba(255,140,0,0.1)'
          }],
        }
      },
      emphasis: {
        focus: 'series'
      },
      animation: true,
    }
  ],
});

const showPreviewBanner = ref(true);

// 滚动事件处理函数
const handleScroll = () => {
  // 检查滚动条位置是否在顶部
  showPreviewBanner.value = window.scrollY === 0;
};

// 添加滚动事件监听器
onMounted(() => {
  window.addEventListener('scroll', handleScroll);
});

// 移除滚动事件监听器
onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll);
});
const route = useRoute();
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=9b8270bfd9ff96e3672feffda35e673f&lang=zh_cn&units=metric`
    );

    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;

    // cal hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime =
        utc + 1000 * weatherData.data.timezone_offset;
    });

    return weatherData.data;
  } catch (err) {
    console.log(err);
  }
};
const weatherData = await getWeatherData();

console.log(weatherData);

const router = useRouter();
const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities.filter(
    (city) => city.id !== route.query.id
  );
  localStorage.setItem(
    "savedCities",
    JSON.stringify(updatedCities)
  );
  router.push({
    name: "home",
  });
};
</script>
<style scoped>
.bgopacity {
  opacity: 0;
}
.chart {
  height: 30vh;
  background-color: transparent;
}
</style>