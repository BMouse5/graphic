<template>
    <div class="chart-container">
      <div v-if="selectedItem" class="selected-item-container">
        <table class="my-table">
          <thead>
            <tr>
              <th>Показатель</th>
              <th>Текущий день</th>
              <th>Вчера</th>
              <th>Неделю назад</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>{{ selectedItem.indicator }}</td>
              <td @click="startEditing('today')">
                <span v-if="!editing || editing.field !== 'today'">{{ formatNumber(selectedItem.today) }}</span>
                <input 
                  v-else
                  v-model.number="editing.value"
                  @blur="saveEditing"
                  @keyup.enter="saveEditing"
                  @keyup.esc="cancelEditing"
                  type="number"
                  class="edit-input"
                  autofocus
                >
              </td>
              <td 
                @click="startEditing('yesterday')"
                :class="{
                  positiveBack: getPercentChange(selectedItem) > 0,
                  negativeBack: getPercentChange(selectedItem) < 0
                }"
              >
                <div class="flex-cell">
                  <span v-if="!editing || editing.field !== 'yesterday'">{{ formatNumber(selectedItem.yesterday) }}</span>
                  <input 
                    v-else
                    v-model.number="editing.value"
                    @blur="saveEditing"
                    @keyup.enter="saveEditing"
                    @keyup.esc="cancelEditing"
                    type="number"
                    class="edit-input"
                    autofocus
                  >
                  <span class="percent" :class="{
                    positive: getPercentChange(selectedItem) > 0,
                    negative: getPercentChange(selectedItem) < 0
                  }">
                    {{ getPercentChange(selectedItem) > 0 ? '+' : '' }}{{ getPercentChange(selectedItem) }}%
                  </span>
                </div>
              </td>
              <td @click="startEditing('TodayDayWeek')">
                <span v-if="!editing || editing.field !== 'TodayDayWeek'">{{ formatNumber(selectedItem.TodayDayWeek) }}</span>
                <input 
                  v-else
                  v-model.number="editing.value"
                  @blur="saveEditing"
                  @keyup.enter="saveEditing"
                  @keyup.esc="cancelEditing"
                  type="number"
                  class="edit-input"
                  autofocus
                >
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="chart-wrapper">
        <canvas ref="chart"></canvas>
      </div>
    </div>
</template>
  
<script setup>
import Chart from 'chart.js/auto'
import { onMounted, ref, watch } from 'vue'
  
const selectedItem = ref(null)
const chart = ref(null)
let chartInstance = null
const editing = ref(null)
  
const formatNumber = (num) => {
    return new Intl.NumberFormat('ru-RU').format(num)
}
  
const getPercentChange = (item) => {
    if (!item || item.yesterday === 0) return 0
    const change = ((item.today - item.yesterday) / item.yesterday) * 100
    return Math.round(change * 10) / 10
}
  
const startEditing = (field) => {
    if (!selectedItem.value || (editing.value && editing.value.field === field)) return
    editing.value = {
      field,
      value: selectedItem.value[field]
    }
}
  
const saveEditing = () => {
    if (editing.value && selectedItem.value) {
      selectedItem.value[editing.value.field] = editing.value.value
      editing.value = null
      updateChart()
    }
}
  
const cancelEditing = () => {
  editing.value = null
}
  
const createChart = () => {
    if (chartInstance) {
      chartInstance.destroy()
    }
  
    chartInstance = new Chart(chart.value, {
      type: 'line',
      data: {
        labels: ['Неделю назад', 'Вчера', 'Сегодня'],
        datasets: [{
          label: selectedItem.value?.indicator || 'Выберите показатель',
          data: [
            selectedItem.value?.TodayDayWeek || 0,
            selectedItem.value?.yesterday || 0,
            selectedItem.value?.today || 0
          ],
          backgroundColor: 'rgba(75, 192, 192, 0.2)',
          borderColor: 'rgba(75, 192, 192, 1)',
          borderWidth: 2,
          tension: 0.4,
          fill: true,
          pointBackgroundColor: 'rgba(75, 192, 192, 1)',
          pointRadius: 5,
          pointHoverRadius: 7
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          title: {
            display: true,
            text: 'Динамика показателя',
            font: {
              size: 16
            }
          },
          tooltip: {
            callbacks: {
              label: function(context) {
                return `${context.dataset.label}: ${formatNumber(context.raw)}`
              }
            }
          }
        },
        scales: {
          y: {
            beginAtZero: false,
            ticks: {
              callback: function(value) {
                return formatNumber(value)
            }
          }
        }
      }
    }
  })
}
  
const updateChart = () => {
    if (chartInstance && selectedItem.value) {
      chartInstance.data.datasets[0].data = [
        selectedItem.value.TodayDayWeek,
        selectedItem.value.yesterday,
        selectedItem.value.today
    ]
    chartInstance.update()
  }
}
  
watch(selectedItem, (newItem) => {
    if (newItem) {
      createChart()
    }
})
  
defineExpose({
    setSelectedItem(item) {
      selectedItem.value = item
    }
})
</script>
  
<style scoped>
.chart-container {
    display: flex;
    flex-direction: column;
    gap: 20px;
    height: 100%;
}
  
.selected-item-container {
    width: 100%;
    overflow-x: auto;
}
  
.chart-wrapper {
    position: relative;
    height: 400px;
    width: 100%;
}
  
.my-table {
    width: 100%;
    border-collapse: separate;
    border-spacing: 10px;
}
  
.my-table tr {
    cursor: pointer;
    transition: all 0.3s ease-in-out;
    background-color: rgb(240, 240, 240);
}
  
.my-table td {
    padding: 8px 25px;
    text-align: left;
    position: relative;
}
  
.my-table td:hover {
    background-color: rgba(0, 0, 0, 0.05);
}
  
.my-table td:nth-child(2) {
    background-color: rgba(0, 0, 255, 0.137);
}
  
.my-table th, .my-table td {
    padding: 8px;
    text-align: left;
}
  
.my-table th {
    background-color: #f2f2f2;
}
  
.flex-cell {
    display: flex;
    justify-content: space-between;
    align-items: center;
}
  
.percent {
    font-size: 0.9em;
    margin-left: 8px;
    font-weight: 500;
}
  
.positive {
    color: #4CAF50;
}
  
.negative {
    color: #F44336;
}
  
.negativeBack {
    background-color: #f4433644;
}
  
.positiveBack {
    background-color: #4caf4f4e;
}
  
.edit-input {
    width: 100%;
    padding: 4px;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: inherit;
    position: absolute;
    left: 0;
    top: 0;
    width: calc(100% - 16px);
    margin: 3px 8px;
    height: calc(100% - 8px);
    box-sizing: border-box;
}
</style>