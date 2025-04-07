<template>
    <table class="my-table">
      <tbody>
        <tr v-for="item in items" :key="item.indicator" @click="emitItemData(item)">
          <td>{{ item.indicator }}</td>
          <td>{{ formatNumber(item.today) }}</td>
          <td :class="{positiveBack: getPercentChange(item) > 0 , negativeBack: getPercentChange(item) < 0}">
            <div class="flex-cell">
              <span>{{ formatNumber(item.yesterday) }}</span>
              <span class="percent" :class="{ positive: getPercentChange(item) > 0, negative: getPercentChange(item) < 0 }">
                {{ getPercentChange(item) > 0 ? '+' : '' }}{{ getPercentChange(item) }}%
              </span>
            </div>
          </td>
          <td>{{ formatNumber(item.TodayDayWeek) }}</td>
        </tr>
      </tbody>
    </table>
  </template>
  
<script setup>
import { ref, defineEmits } from "vue"
 
const items = ref([
    { indicator: 'Выручка, руб.', today: 500521, yesterday: 480521, TodayDayWeek: 4805121 },
    { indicator: 'Наличные', today: 300000, yesterday: 3000000, TodayDayWeek: 300000 },
    { indicator: 'Безналичный расчет', today: 100000, yesterday: 1000000, TodayDayWeek: 100000 },
    { indicator: 'Кредитные карты', today: 100521, yesterday: 100521, TodayDayWeek: 100521 },
    { indicator: 'Средний чек, руб.', today: 1300, yesterday: 900, TodayDayWeek: 900 },
    { indicator: 'Средний гость, руб.', today: 1200, yesterday: 800, TodayDayWeek: 800 },
    { indicator: 'Удаления из чека(после оплаты), руб.', today: 1000, yesterday: 1100, TodayDayWeek: 900 },
    { indicator: 'Удаления из чека(до оплаты), руб', today: 1300, yesterday: 1300, TodayDayWeek: 900 },
    { indicator: 'Количество чеков', today: 34, yesterday: 36, TodayDayWeek: 34 },
    { indicator: 'Количество гостей', today: 34, yesterday: 36, TodayDayWeek: 32 },
])
  
const formatNumber = (num) => {
  return new Intl.NumberFormat('ru-RU').format(num)
}
  
const getPercentChange = (item) => {
  if (item.yesterday === 0) return 0 // чтобы избежать деления на ноль
  const change = ((item.today - item.yesterday) / item.yesterday) * 100
  return Math.round(change * 10) / 10 // округляем до 1 знака после запятой
  }

const emit = defineEmits(['item-clicked']);

const emitItemData = (item) => {
    emit('item-clicked', item)
}
</script>
  
<style scoped>
.my-table {
    width: 100%;
    border-collapse: separate;
    border-spacing: 10px 10px;
}
  
.my-table tr {
    cursor: pointer;
    transition: all 0.3s ease-in-out;
    background-color: rgb(240, 240, 240);
}
  
.my-table tr:hover {
    transform: scale(1.02);
    background-color: rgb(228, 228, 228);
}
  
.my-table td {
    padding: 8px 25px;
    text-align: left;
}

.my-table td:nth-child(2) {
    background-color: rgba(0, 0, 255, 0.137);
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
    color: #4CAF50; /* Зеленый для положительных значений */
}
  
.negative {
    color: #F44336; /* Красный для отрицательных значений */
}

.negativeBack {
    background-color:#f4433644;
}

.positiveBack {
    background-color: #4caf4f4e;
}
</style>