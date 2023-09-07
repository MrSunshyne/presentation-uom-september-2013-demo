<template>
  <div class="bg-slate-800 h-full text-white">
    <div class="max-w-6xl mx-auto p-16">
      <div class="text-4xl pb-6">Power cuts today</div>
      <div class="grid grid-cols-2 gap-8">
        <div v-for="info in dataToIsToday">
          <div
            class="bg-slate-700 p-4 rounded-lg shadow-md flex flex-col gap-2"
            :class="{ 'opacity-50': isPowerOff(info) }"
          >
            <div class="font-bold text-2xl">
              {{ info.locality }}
            </div>

            <div class="text-sm">
              {{ new Date(info.from).toLocaleDateString() }}
            </div>

            <div>
              {{ new Date(info.from).toLocaleTimeString() }} to
              {{ new Date(info.to).toLocaleTimeString() }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';

const dataset = ref({});
const selectedRegion = ref('portlouis');

function flatten(obj) {
  return Object.values(obj).flat();
}

function fetchData() {
  let endpoint =
    'https://raw.githubusercontent.com/MrSunshyne/mauritius-dataset-electricity/main/data/power-outages.json';

  fetch(endpoint)
    .then((response) => response.json())
    .then((res) => {
      console.log(res);
      dataset.value = res;
    })
    .catch((error) => console.log(error));
}

function isPowerOff(info) {
  let now = new Date().getTime();
  let dateFrom = new Date(info.from).getTime();
  let dateTo = new Date(info.to).getTime();

  return now >= dateFrom && now <= dateTo;
}

const datasetForSelectedRegion = computed(() => {
  return dataset.value[selectedRegion.value];
});

const dataToIsToday = computed(() => {
  return flatten(dataset.value).filter((item) => {
    let itemDate = new Date(item.to).toLocaleDateString();
    let todayDate = new Date().toLocaleDateString();

    return itemDate === todayDate;
  });
});

onMounted(() => {
  fetchData();
});
</script>
