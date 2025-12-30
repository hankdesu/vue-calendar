<script setup lang="ts">
import { computed, ref } from 'vue'

enum OPERATIONS {
  DECREMENT,
  INCREMENT,
}
const daysOfWeek = ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat']
const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
const selectedDate = ref(new Date())
const year = ref(new Date().getFullYear())
const month = ref(new Date().getMonth())
const daysOfMonth = computed(() =>
  year.value % 4 === 0
    ? [31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]
    : [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31],
)
const firstDayOfMonth = computed(() => new Date(`${year.value}-${month.value + 1}-01`).getDay())

function onYearClick(operation: OPERATIONS) {
  if (operation === OPERATIONS.INCREMENT) {
    year.value++
  } else if (operation === OPERATIONS.DECREMENT && year.value > 0) {
    year.value--
  }
}

function onMonthClick(operation: OPERATIONS) {
  if (operation === OPERATIONS.INCREMENT) {
    month.value++
  } else if (operation === OPERATIONS.DECREMENT) {
    month.value--
  }
  if (month.value >= 12) {
    month.value = 0
    year.value++
  } else if (month.value < 0) {
    month.value = 11
    year.value--
  }
}

function isActive(n: number) {
  const date = new Date(year.value, month.value, n)

  return selectedDate.value.getTime() === date.getTime()
}

function isToday(n: number) {
  const date = new Date(year.value, month.value, n)
  const today = new Date()

  return (
    today.getFullYear() === date.getFullYear() &&
    today.getMonth() === date.getMonth() &&
    today.getDate() === date.getDate()
  )
}

function onDateClick(n: number) {
  const date = new Date(year.value, month.value, n)
  selectedDate.value = date
}
</script>

<template>
  <section class="page">
    <main class="flex-page">
      <div class="container">
        <div class="header">
          <div class="btn-group">
            <button @click="onYearClick(OPERATIONS.DECREMENT)">&lt;&lt;</button>
            <button @click="onMonthClick(OPERATIONS.DECREMENT)">&lt;</button>
          </div>
          <div class="info">
            <span>{{ year }}</span>
            <span>{{ months[month] }}</span>
          </div>
          <div class="btn-group">
            <button @click="onMonthClick(OPERATIONS.INCREMENT)">></button>
            <button name="increment" @click="onYearClick(OPERATIONS.INCREMENT)">>></button>
          </div>
        </div>
        <div class="content">
          <div class="date" v-for="(day, index) in daysOfWeek" :key="index">
            {{ day }}
          </div>
          <div class="date" v-for="(_, index) in firstDayOfMonth" :key="index"></div>
          <div
            class="date clickable"
            :class="{ active: isActive(n), today: isToday(n) }"
            v-for="n in daysOfMonth[month]"
            :key="n"
            @click="onDateClick(n)"
          >
            {{ n }}
          </div>
        </div>
      </div>
    </main>
  </section>
</template>

<style scoped>
.page {
  min-height: 100vh;
}

.flex-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.container {
  height: 50vh;
  width: 50vw;
  box-shadow: 5px 3px 15px -5px #a5a5a5;
  display: flex;
  flex-direction: column;
  padding: 20px;
  gap: 10px;
  border-radius: 20px;
}

.container .header {
  height: 40px;
  min-height: 40px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.container .header .info {
  display: flex;
  gap: 10px;
}

.container .header .btn-group {
  display: flex;
  gap: 10px;
}

.container .header .btn-group button {
  border-radius: 8px;
  background: white;
  box-shadow: 5px 5px 15px -8px #a5a5a5;
  border: none;
  width: 50px;
  height: 30px;
  cursor: pointer;
}

.container .header .btn-group button:active {
  background: #4e61ff;
  color: white;
  border: none;
}

.container .content {
  height: 100%;
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  grid-auto-rows: minmax(40px, 1fr);
}

.container .content .date {
  display: flex;
  align-items: start;
  justify-content: center;
  border-radius: 10px;
  padding: 5px;
}

.container .content .date.active {
  background: #4e61ff !important;
  color: white;
}

.container .content .date.today {
  background: #ffd643;
  color: white;
}

.clickable {
  cursor: pointer;
}
</style>
