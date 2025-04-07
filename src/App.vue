<template>
  <div id="app">
    <div class="container">
      <ChartComp ref="chartComp"></ChartComp>
      <TableComp @item-clicked="handleItemClick"></TableComp>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ChartComp from '../src/components/ChartComp.vue'
import TableComp from './components/TableComp.vue'

const chartComp = ref(null)

const handleItemClick = (item) => {
  chartComp.value.setSelectedItem(item)
}
</script>
<style scoped>
.container {
  max-width: 1440px;
  padding: 20px;
}
</style>
