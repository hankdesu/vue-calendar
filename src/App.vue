<script setup lang="ts">
import { computed, ref } from 'vue'
import NeumorphicButton from './components/NeumorphicButton.vue'
import NeumorphicDropdown from './components/NeumorphicDropdown.vue'

enum OPERATIONS {
  DECREMENT,
  INCREMENT,
}
const daysOfWeek = ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat']
const months = [
  'January',
  'February',
  'March',
  'April',
  'May',
  'June',
  'July',
  'August',
  'September',
  'October',
  'November',
  'December',
]
const selectedDate = ref<Date | null>(null)
const yearOptions = Array.from(new Array(3000)).map((_, index) => ({
  text: `${index + 1}`,
  value: index + 1,
}))
const year = ref(new Date().getFullYear())
const month = ref(new Date().getMonth())
const daysOfMonth = computed(() =>
  year.value % 4 === 0
    ? [31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]
    : [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31],
)
const firstDayOfMonth = computed(() => new Date(`${year.value}-${month.value + 1}-01`).getDay())
const activeDate = computed(() => {
  if (!selectedDate.value) return null

  if (
    selectedDate.value.getFullYear() !== year.value ||
    selectedDate.value.getMonth() !== month.value
  ) {
    return null
  }

  return selectedDate.value.getDate()
})

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

  if (date.getTime() === selectedDate.value?.getTime()) {
    selectedDate.value = null
  } else {
    selectedDate.value = date
  }
}
</script>

<template>
  <section class="page">
    <main class="flex-page">
      <div class="tool-bar">
        <NeumorphicButton
          text="<"
          :height="60"
          :width="60"
          @click="onMonthClick(OPERATIONS.DECREMENT)"
        />
        <NeumorphicButton
          text=">"
          :height="60"
          :width="60"
          @click="onMonthClick(OPERATIONS.INCREMENT)"
        />
        <NeumorphicDropdown :options="yearOptions" v-model="year" />
      </div>
      <div class="container">
        <div class="header">
          <!-- <div class="btn-group">
            <button @click="onYearClick(OPERATIONS.DECREMENT)">Prev year</button>
          </div> -->
          <div class="info">
            <span>{{ year }}</span>
            <span>{{ months[month] }}</span>
          </div>
          <!-- <div class="btn-group">
            <button @click="onMonthClick(OPERATIONS.INCREMENT)">Next month</button>
          </div> -->
        </div>
        <div class="content">
          <div class="date header" v-for="(day, index) in daysOfWeek" :key="index">
            {{ day }}
          </div>
          <div class="date" v-for="(_, index) in firstDayOfMonth" :key="index"></div>
          <div
            class="date clickable"
            :class="{ active: n === activeDate, today: isToday(n) }"
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
  background: #f1f1f3;
}

.flex-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 40px;
}

.tool-bar {
  display: flex;
  gap: 40px;
}

.container {
  height: 50vh;
  width: 50vw;
  box-shadow:
    -12px -12px 15px 2px #f8f8f8,
    10px 10px 15px 2px #d9dadf;
  display: flex;
  flex-direction: column;
  padding: 20px;
  gap: 10px;
  border-radius: 35px;
  max-width: 650px;
  background: #f1f1f3;
  color: #333333;
}

.container .header {
  height: 40px;
  min-height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container .header .info {
  display: flex;
  gap: 10px;
  font-weight: 600;
  font-size: 20px;
}

.container .header .btn-group {
  display: flex;
  gap: 10px;
}

.container .header .btn-group button {
  border-radius: 8px;
  background: #4e61ff;
  box-shadow: 7px 5px 20px -8px #3345ff;
  border: none;
  height: 30px;
  cursor: pointer;
  color: white;
}

.container .header .btn-group button:active {
  background: #2737c9;
  box-shadow: 8px 5px 20px -8px #2737c9;
  color: white;
  border: none;
}

.container .content {
  height: 100%;
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.container .content .header {
  font-weight: 500;
}

.container .content .date {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 100%;
  padding: 5px;
  height: 50px;
  width: 50px;
  align-self: center;
  justify-self: center;
}

.container .content .date.active {
  background: #4e61ff !important;
  color: white;
}

.container .content .date.today {
  box-shadow:
    inset 6px 6px 15px 0px #e0e0e0c9,
    inset -8px -9px 15px 0px #ffffff;
}

.clickable {
  cursor: pointer;
}
</style>
