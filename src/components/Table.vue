<script setup>
import { ref, onMounted } from 'vue'

const data = ref([])
onMounted(async () => {
  try {
    const response = await fetch('https://server-small-butterfly-8681.fly.dev')
    const jsonData = await response.json()
    data.value = jsonData
  } catch (error) {
    console.log(error)
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
      data.value.sort((a, b) => b[2] - a[2])
    } else if (key === 'gold') {
      data.value.sort((a, b) => b[3] - a[3])
    } else if (key === 'total') {
      data.value.sort((a, b) => b[4] - a[4])
    } else if (key === 'athletes') {
      data.value.sort((a, b) => b[5] - a[5])
    }
  } else {
    if (key === 'country') {
      data.value.sort((a, b) => b[0].localeCompare(a[0]))
    } else if (key === 'bronze') {
      data.value.sort((a, b) => a[1] - b[1])
    } else if (key === 'silver') {
      data.value.sort((a, b) => a[2] - b[2])
    } else if (key === 'gold') {
      data.value.sort((a, b) => a[3] - b[3])
    } else if (key === 'total') {
      data.value.sort((a, b) => a[4] - b[4])
    } else if (key === 'athletes') {
      data.value.sort((a, b) => a[5] - b[5])
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
    <tbody>
      <tr v-for="item in data" class="border border-slate-300 bg-stone-50 hover:bg-zinc-100">
        <td>{{ item[0] }}</td>
        <td class='text-center'>{{ item[1].toFixed(2) }}</td>
        <td class='text-center'>{{ item[2].toFixed(2) }}</td>
        <td class='text-center'>{{ item[3].toFixed(2) }}</td>
        <td class='text-center'>{{ item[4].toFixed(2) }}</td>
        <td class='text-center'>{{ item[5] }}</td>
      </tr>
    </tbody>
  </table>
</template>

