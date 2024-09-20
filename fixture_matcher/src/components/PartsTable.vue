<template>
  <div>
    <h1>Fixture Matcher Tool</h1>
    <div v-if="loading">Loading data...</div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>
      <!-- Search input field -->
      <input type="text" v-model="searchQuery" placeholder="Search..." />

      <!-- Field selection control for searching -->
      <select v-model="searchField">
        <option value="Both">Search Both</option>
        <option value="Part">Search Part Number</option>
        <option value="Mask">Search Fixturing Plug</option>
      </select>

      <!-- Sort selection control -->
      <select v-model="sortField">
        <option value="Part">Sort by Part Number</option>
        <option value="Mask">Sort by Fixturing Plug</option>
      </select>

      <!-- Display a message if no matching results -->
      <div v-if="filteredPartsData.length === 0">
        No matching results.
      </div>

      <!-- Table displaying filtered and sorted data -->
      <table v-else>
        <thead>
          <tr>
            <th>Part Number</th>
            <th>Fixturing Plug</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in filteredPartsData" :key="index">
            <td>{{ item.Part }}</td>
            <td>{{ item.Mask }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import partsDataJson from '../data/maskmap.json' // Adjust the path as necessary

const partsData = ref(partsDataJson)
const searchQuery = ref('')
const searchField = ref('Both')
const sortField = ref('Part')

// Computed property remains the same
const filteredPartsData = computed(() => {
  let filteredData = []

  // Filtering logic
  if (!searchQuery.value) {
    filteredData = partsData.value
  } else {
    const query = searchQuery.value.toLowerCase()
    filteredData = partsData.value.filter(item => {
      if (searchField.value === 'Mask') {
        return item.Mask.toLowerCase().includes(query)
      } else if (searchField.value === 'Part') {
        return item.Part.toLowerCase().includes(query)
      } else {
        return (
          item.Mask.toLowerCase().includes(query) ||
          item.Part.toLowerCase().includes(query)
        )
      }
    })
  }

  // Sorting logic
  return filteredData.slice().sort((a, b) => {
    const fieldA = a[sortField.value]?.toLowerCase() || ''
    const fieldB = b[sortField.value]?.toLowerCase() || ''
    if (fieldA < fieldB) return -1
    if (fieldA > fieldB) return 1
    return 0
  })
})
</script>

<style scoped>
  /* Basic styling for the table */
  table {
    border-collapse: collapse;
    width: 100%;
    margin-top: 10px;
  }
  
  thead th {
    background-color: #f2f2f2;
    text-align: left;
  }

  @media (prefers-color-scheme: dark) {
    thead th {
      background-color: #333;
      color: #fff;
    }
  }
  
  th, td {
    border: 1px solid #ddd;
    padding: 8px;
  }
  
  h1 {
    margin-bottom: 0;
  }

  /* Styling for input and select elements */
  input[type="text"], select {
    width: 100%;
    padding: 8px;
    margin-top: 10px;
    box-sizing: border-box;
  }

  input[type="text"]::placeholder {
    color: #aaa;
  }

  select {
    margin-top: 5px;
  }
</style>