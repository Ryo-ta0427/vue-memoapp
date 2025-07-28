<template>
<div class="calendar">
  
  <h1><button @click="prevMonth" class="arrow-btn">◄</button>
  {{  displayYear }}年 {{ displayMonth +1 }}月のカレンダー
  <button @click="nextMonth" class="arrow-btn">►</button>
  </h1>
  <button @click="goToToday">今日に戻る</button>

  <div class="grid">
    <div
    v-for="day in daysInMonth"
    :key="day"
    class="day"
    :class="{ today: isToday(day) }"
    @click="selectDate(day)"
    >
 <div class="day-number">{{ day }}</div>
  <div v-if="memos[currentKey]?.[day]" class="memo">{{ memos[currentKey][day] }}
  </div>
  
   <div v-if="selectedDay === day" class="input-area">
    <input v-model="memoText" placeholder="メモを入力" />
    <div class="button-area">
    <button @click.stop="saveMemo(day)">保存</button>
    <button v-if="memos[currentKey]?.[day]" class="delete-btn" @click.stop="deleteMemo(day)">削除</button>
   </div>
  </div>
</div>
</div>
</div>


</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

const currentDate = new Date()
const displayYear = ref(currentDate.getFullYear())
const displayMonth = ref(currentDate.getMonth()) 

const todayDate = new Date()
const todayYear = todayDate.getFullYear()
const todayMonth = todayDate.getMonth()
const todayDay = todayDate.getDate()

const currentKey = computed(() => {
  return `${displayYear.value}-${String(displayMonth.value + 1).padStart(2, '0')}`
})

const daysInMonth = computed(() => {
  return new Date(displayYear.value, displayMonth.value + 1, 0).getDate()
})

function prevMonth() {
  if (displayMonth.value === 0) {
    displayMonth.value = 11
    displayYear.value--
  } else {
    displayMonth.value--
  }
  selectedDay.value = null
}

function nextMonth() {
  if (displayMonth.value === 11) {
    displayMonth.value = 0
    displayYear.value++
  }else {
    displayMonth.value++
  }
  selectedDay.value =null
}

const memos =ref({})
const selectedDay =ref(null)
const memoText = ref('')

function selectDate(day) {
 selectedDay.value = day
 memoText.value = memos.value[currentKey.value]?.[day] || ''
}

function saveMemo(day) {
  if (!memos.value[currentKey.value]) {
    memos.value[currentKey.value] = {}
  }
  memos.value[currentKey.value][day] = memoText.value
  selectedDay.value = null
  memoText.value = ''
localStorage.setItem('calendar-memos', JSON.stringify(memos.value))
}

function deleteMemo(day) {
  console.log("削除ボタンが押されたよ", day)
  if (memos.value[currentKey.value]) {
  delete memos.value[currentKey.value][day]
  localStorage.setItem('calendar-memos', JSON.stringify(memos.value))

  if (selectedDay.value === day) {
    selectedDay.value = null
    memoText.value = ''
  }
}
}

function goToToday() {
  displayYear.value = todayYear
  displayMonth.value = todayMonth
  selectedDay.value = todayDay
  memoText.value = memos.value[currentKey.value]?.[todayDay] || ''
}

function isToday(day) {
  return (
    displayYear.value === todayYear &&
    displayMonth.value === todayMonth &&
   day === todayDay
  )
}


onMounted(() =>{
  const saved = localStorage.getItem('calendar-memos')
  if (saved) {
    memos.value =JSON.parse(saved)
  }
})
</script>

<style scoped>
.calendar {
  text-align: center;
  font-family: "メイリオ", sans-serif;
}

.grid {
  display: grid;
  grid-template-columns: repeat(7,1fr);
  gap: 8px;
  padding: 20px;
}

.day {
  background-color: #ffffff;
  border: 1px solid #007ccc;
  color: #333333;
  font-weight: bold;
  padding: 16px;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s;
}

.day:hover {
  background-color: #e6f2ff;
}

.day-number {
  font-size: 16px;
}

.memo {
  font-size: 13px;
  color:#007700;
  margin-top: 5px;
  word-break: break-word;
}

.input-area {
  display: flex;
  flex-direction: column;
  gap: 6px;
  margin-top: 6px;
}

input {
  padding: 4px;
  font-size: 14px;
}


.delete-btn {
  padding: 6px 12px;              
  background-color: #ffcccc;
  color: #cc0000;
  font-weight: bold;
  border: none;
  border-radius: 4px;
}



 button {
 font-size: 13px;
  padding: 6px 12px;
  border-radius: 4px;
  border: none;
  cursor: pointer;
  background-color: #007acc;
  color: white;
 }

.arrow-btn {
  background-color: transparent;
  border: none;
  color: white;
  font-size: 24px;
  cursor: pointer;
}

.arrow-btn:hover {
  color: #ffd700;
}

.button-area {
  display: flex;
  gap: 8px;
  justify-content: center;
 align-items: center;
 flex-wrap: wrap;
}

.today {
  background-color: #fffacd !important;
  border: 2px solid #ffa500 !important;
}

</style>

