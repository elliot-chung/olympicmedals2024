<script setup>
import { ref, onMounted } from "vue";
import athleteData from "../assets/athletes.json";
import medalData from "../assets/medals.json";
import countryData from "../assets/countries.json";

const loadState = ref("loading");
const data = ref([]);
onMounted(async () => {
  try {
    data.value = medalsPer100Athletes();
    loadState.value = "done";
  } catch (error) {
    console.log(error);
    loadState.value = "error";
  }
});

const medalsPer100Athletes = () => {
  const epsilon = 0.00001;
  const output = [];
  // 'AIN' team is missing from the medal data
  // AIN is the country for Belarus and Russia since both country's athletes have been banned from the Olympics due to doping (and the Russian war on Ukraine)
  // The latter reason seems to be the reason why microsoft is not reporting the medal data for AIN
  for (const medalResult of medalData) {
    const bronzeCount = medalResult["medalsWon"]["Bronze"]
      ? medalResult["medalsWon"]["Bronze"]
      : epsilon;
    const silverCount = medalResult["medalsWon"]["Silver"]
      ? medalResult["medalsWon"]["Silver"]
      : epsilon;
    const goldCount = medalResult["medalsWon"]["Gold"]
      ? medalResult["medalsWon"]["Gold"]
      : epsilon;
    const totalCount = medalResult["medalsWon"]["Total"]
      ? medalResult["medalsWon"]["Total"]
      : epsilon;

    const countryKey = medalResult["team"]["shortName"]["rawName"];
    const athleteCount = athleteData[countryKey] || 1;
    const countryName = countryData[countryKey] || "Unknown";

    const bronze_per_athlete = bronzeCount / athleteCount;
    const silver_per_athlete = silverCount / athleteCount;
    const gold_per_athlete = goldCount / athleteCount;
    const total_per_athlete = totalCount / athleteCount;

    const bronze_per_100 = bronze_per_athlete * 100;
    const silver_per_100 = silver_per_athlete * 100;
    const gold_per_100 = gold_per_athlete * 100;
    const total_per_100 = total_per_athlete * 100;

    const item = [
      countryName,
      bronze_per_100,
      bronzeCount,
      silver_per_100,
      silverCount,
      gold_per_100,
      goldCount,
      total_per_100,
      totalCount,
      athleteCount,
    ];
    output.push(item);
  }
  console.log(output);
  return output;
};

const sortKey = ref("");
const sortUp = ref(true);

const sortBy = (key) => {
  if (key === sortKey.value) {
    sortUp.value = !sortUp.value;
  } else {
    sortUp.value = true;
  }
  sortKey.value = key;
  if (sortUp.value) {
    if (key === "country") {
      data.value.sort((a, b) => a[0].localeCompare(b[0]));
    } else if (key === "bronze") {
      data.value.sort((a, b) => b[1] - a[1]);
    } else if (key === "silver") {
      data.value.sort((a, b) => b[3] - a[3]);
    } else if (key === "gold") {
      data.value.sort((a, b) => b[5] - a[5]);
    } else if (key === "total") {
      data.value.sort((a, b) => b[7] - a[7]);
    } else if (key === "athletes") {
      data.value.sort((a, b) => b[9] - a[9]);
    }
  } else {
    if (key === "country") {
      data.value.sort((a, b) => b[0].localeCompare(a[0]));
    } else if (key === "bronze") {
      data.value.sort((a, b) => a[1] - b[1]);
    } else if (key === "silver") {
      data.value.sort((a, b) => a[3] - b[3]);
    } else if (key === "gold") {
      data.value.sort((a, b) => a[5] - b[5]);
    } else if (key === "total") {
      data.value.sort((a, b) => a[7] - b[7]);
    } else if (key === "athletes") {
      data.value.sort((a, b) => a[9] - b[9]);
    }
  }
};
</script>

<template>
  <table class="table-fixed border-collapse border border-slate-300 shadow-lg">
    <thead>
      <tr class="border-2 border-slate-300 bg-zinc-200">
        <th @click="sortBy('country')" class="cursor-pointer hover:scale-110">
          Country
        </th>
        <th
          @click="sortBy('bronze')"
          class="min-w-36 cursor-pointer px-2 hover:scale-110"
        >
          Bronze Medals Per 100 Athletes
        </th>
        <th
          @click="sortBy('silver')"
          class="min-w-36 cursor-pointer px-2 hover:scale-110"
        >
          Silver Medals Per 100 Athletes
        </th>
        <th
          @click="sortBy('gold')"
          class="min-w-36 cursor-pointer px-2 hover:scale-110"
        >
          Gold Medals Per 100 Athletes
        </th>
        <th
          @click="sortBy('total')"
          class="min-w-36 cursor-pointer px-2 hover:scale-110"
        >
          Total Medals Per 100 Athletes
        </th>
        <th
          @click="sortBy('athletes')"
          class="min-w-36 cursor-pointer px-2 hover:scale-110"
        >
          Total Athletes
        </th>
      </tr>
    </thead>
    <tbody v-if="loadState === 'done'">
      <tr
        v-for="item in data"
        class="border border-slate-300 bg-stone-50 hover:bg-zinc-100"
      >
        <td>{{ item[0] }}</td>
        <td class="text-center">
          {{ item[1].toFixed(2) + " (" + item[2].toFixed(0) + ")" }}
        </td>
        <td class="text-center">
          {{ item[3].toFixed(2) + " (" + item[4].toFixed(0) + ")" }}
        </td>
        <td class="text-center">
          {{ item[5].toFixed(2) + " (" + item[6].toFixed(0) + ")" }}
        </td>
        <td class="text-center">
          {{ item[7].toFixed(2) + " (" + item[8].toFixed(0) + ")" }}
        </td>
        <td class="text-center">{{ item[9] }}</td>
      </tr>
    </tbody>
    <tbody v-else-if="loadState === 'error'">
      <tr>
        <td colspan="6">
          <div class="flex justify-center">
            <div class="text-center">
              <p class="text-2xl font-bold">Error</p>
              <p class="text-lg">
                Something went wrong while loading the data.
              </p>
            </div>
          </div>
        </td>
      </tr>
    </tbody>
    <tbody v-else>
      <tr>
        <td colspan="6">
          <div class="flex justify-center">
            <div class="text-center">
              <p class="text-2xl font-bold">Loading...</p>
              <p class="text-lg">Please wait while the data is being loaded.</p>
            </div>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
</template>
