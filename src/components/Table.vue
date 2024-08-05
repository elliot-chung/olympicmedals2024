<script setup>
import { ref, onMounted } from 'vue'

const loadState = ref("loading")
const data = ref([])
onMounted(async () => {
  try {
    const response = await fetch('https://server-small-butterfly-8681.fly.dev', { signal: AbortSignal.timeout(7000) })
    const jsonData = await response.json()
    data.value = jsonData
    loadState.value = "done"
  } catch (error) {
    console.log(error)
    loadState.value = "error"
  } 
})

const sortKey = ref('')
const sortUp = ref(true)

const sortBy = (key) => {
  if (key === sortKey.value) {
    sortUp.value = !sortUp.value
  } else {
    sortUp.value = true
  }
  sortKey.value = key
  if (sortUp.value) {
    if (key === 'country') {
      data.value.sort((a, b) => a[0].localeCompare(b[0]))
    } else if (key === 'bronze') {
      data.value.sort((a, b) => b[1] - a[1])
    } else if (key === 'silver') {
      data.value.sort((a, b) => b[3] - a[3])
    } else if (key === 'gold') {
      data.value.sort((a, b) => b[5] - a[5])
    } else if (key === 'total') {
      data.value.sort((a, b) => b[7] - a[7])
    } else if (key === 'athletes') {
      data.value.sort((a, b) => b[9] - a[9])
    }
  } else {
    if (key === 'country') {
      data.value.sort((a, b) => b[0].localeCompare(a[0]))
    } else if (key === 'bronze') {
      data.value.sort((a, b) => a[1] - b[1])
    } else if (key === 'silver') {
      data.value.sort((a, b) => a[3] - b[3])
    } else if (key === 'gold') {
      data.value.sort((a, b) => a[5] - b[5])
    } else if (key === 'total') {
      data.value.sort((a, b) => a[7] - b[7])
    } else if (key === 'athletes') {
      data.value.sort((a, b) => a[9] - b[9])
    } 
  }
}

</script>

<template>
  <table class="table-fixed border-collapse border border-slate-300 shadow-lg">
    <thead>
      <tr class="border-2 border-slate-300 bg-zinc-200">
        <th @click="sortBy('country')" class="cursor-pointer hover:scale-110">Country</th>
        <th @click="sortBy('bronze')" class="cursor-pointer hover:scale-110">Bronze Medals Per 100 Athletes</th>
        <th @click="sortBy('silver')" class="cursor-pointer hover:scale-110">Silver Medals Per 100 Athletes</th>
        <th @click="sortBy('gold')" class="cursor-pointer hover:scale-110">Gold Medals Per 100 Athletes</th>
        <th @click="sortBy('total')" class="cursor-pointer hover:scale-110">Total Medals Per 100 Athletes</th>
        <th @click="sortBy('athletes')" class="cursor-pointer hover:scale-110">Total Athletes</th>
      </tr>
    </thead>
    <tbody v-if="loadState === 'done' && data[0].length === 6">
      <tr v-for="item in data" class="border border-slate-300 bg-stone-50 hover:bg-zinc-100">
        <td>{{ item[0] }}</td>
        <td class='text-center'>{{ item[1].toFixed(2) }}</td>
        <td class='text-center'>{{ item[2].toFixed(2) }}</td>
        <td class='text-center'>{{ item[3].toFixed(2) }}</td>
        <td class='text-center'>{{ item[4].toFixed(2) }}</td>
        <td class='text-center'>{{ item[5] }}</td>
      </tr>
    </tbody>
    <tbody v-if="loadState === 'done' && data[0].length === 10">
      <tr v-for="item in data" class="border border-slate-300 bg-stone-50 hover:bg-zinc-100">
        <td>{{ item[0] }}</td>
        <td class='text-center'>{{ item[1].toFixed(2) + ' (' + item[2].toFixed(0) + ')' }}</td>
        <td class='text-center'>{{ item[3].toFixed(2) + ' (' + item[4].toFixed(0) + ')' }}</td>
        <td class='text-center'>{{ item[5].toFixed(2) + ' (' + item[6].toFixed(0) + ')' }}</td>
        <td class='text-center'>{{ item[7].toFixed(2) + ' (' + item[8].toFixed(0) + ')' }}</td>
        <td class='text-center'>{{ item[9] }}</td>
      </tr>
    </tbody>
    <tbody v-else-if="loadState === 'error'">
      <tr>
        <td colspan="6">
          <div class="flex justify-center">
            <div class="text-center">
              <p class="text-2xl font-bold">Error</p>
              <p class="text-lg">Something went wrong while loading the data.</p>
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

