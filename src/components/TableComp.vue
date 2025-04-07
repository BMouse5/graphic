<template>
    <div class="table-responsive">
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
          <tr v-for="item in items" :key="item.indicator" @click="emitItemData(item)">
            <td data-label="Показатель">{{ item.indicator }}</td>
            <td data-label="Текущий день">{{ formatNumber(item.today) }}</td>
            <td 
              data-label="Вчера"
              :class="{
                positiveBack: getPercentChange(item) > 0,
                negativeBack: getPercentChange(item) < 0
              }"
            >
              <div class="flex-cell">
                <span>{{ formatNumber(item.yesterday) }}</span>
                <span 
                  class="percent" 
                  :class="{ 
                    positive: getPercentChange(item) > 0, 
                    negative: getPercentChange(item) < 0 
                  }"
                >
                  {{ getPercentChange(item) > 0 ? '+' : '' }}{{ getPercentChange(item) }}%
                </span>
              </div>
            </td>
            <td data-label="Неделю назад">{{ formatNumber(item.TodayDayWeek) }}</td>
          </tr>
        </tbody>
      </table>
    </div>
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
    if (item.yesterday === 0) return 0
    const change = ((item.today - item.yesterday) / item.yesterday) * 100
    return Math.round(change * 10) / 10
  }
  
  const emit = defineEmits(['item-clicked'])
  
  const emitItemData = (item) => {
    emit('item-clicked', item)
  }
  </script>
  
  <style scoped>
  .table-responsive {
    width: 100%;
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
  }
  
  .my-table {
    width: 100%;
    border-collapse: collapse;
    margin: 0;
    font-size: 1rem;
  }
  
  .my-table th,
  .my-table td {
    padding: 12px 15px;
    text-align: left;
    border-bottom: 1px solid #e0e0e0;
  }
  
  .my-table th {
    background-color: #f5f5f5;
    font-weight: 600;
    position: sticky;
    top: 0;
  }
  
  .my-table tr {
    transition: background-color 0.2s;
    cursor: pointer;
  }
  
  .my-table tr:hover {
    background-color: #f9f9f9;
  }
  
  .my-table td:nth-child(2) {
    background-color: rgba(0, 0, 255, 0.05);
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
    background-color: #f4433610;
  }
  
  .positiveBack {
    background-color: #4caf5010;
  }
  
  /* Адаптация для мобильных устройств */
  @media (max-width: 768px) {
    .table-responsive {
      width: 100vw;
      margin-left: -20px;
      padding: 0 20px;
    }
    
    .my-table {
      min-width: 600px; /* Минимальная ширина для горизонтального скролла */
    }
    
    .my-table th,
    .my-table td {
      padding: 10px 12px;
      font-size: 0.9rem;
    }
    
    .flex-cell {
      flex-direction: column;
      align-items: flex-start;
      gap: 4px;
    }
    
    .percent {
      margin-left: 0;
    }
  }
  
  /* Альтернативный вариант для очень маленьких экранов */
  @media (max-width: 480px) {
    .table-responsive {
      overflow-x: visible;
      width: 100%;
      margin-left: 0;
      padding: 0;
    }
    
    .my-table {
      min-width: 100%;
      display: block;
    }
    
    .my-table thead {
      display: none;
    }
    
    .my-table tr {
      display: block;
      margin-bottom: 15px;
      border: 1px solid #e0e0e0;
      border-radius: 8px;
      overflow: hidden;
    }
    
    .my-table td {
      display: flex;
      justify-content: space-between;
      align-items: center;
      text-align: right;
      padding: 10px 15px;
      border-bottom: 1px solid #e0e0e0;
    }
    
    .my-table td:last-child {
      border-bottom: none;
    }
    
    .my-table td::before {
      content: attr(data-label);
      font-weight: 600;
      margin-right: 15px;
      color: #666;
    }
    
    .flex-cell {
      width: 100%;
    }
    
    .my-table td:nth-child(2) {
      background-color: transparent;
    }
    
    .positiveBack,
    .negativeBack {
      background-color: transparent !important;
    }
  }
  </style>