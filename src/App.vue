<script setup>
import { ref, computed, watchEffect, onMounted, onBeforeMount, watch } from "vue";
import moment from 'moment';

const day = ref(10)
const balance = ref(0)
let daysInMonth = 0
let nowDay = 0

const perDay = computed(()=>{
  let result = 0
  let daySalary = day.value > daysInMonth ? daysInMonth : +day.value
  if(daySalary <= nowDay){
    result = balance.value/(daySalary + daysInMonth - nowDay)
  }else{
    result = balance.value/(daySalary - nowDay)
  }
  result = result ? Math.floor(result) : 0

  return result
})

const onChangeDay = function() {
  if(day.value > 31 || day.value < 1)
    day.value = 10
}


watch([day, ()=>balance.value], ()=>{
  localStorage.setItem('finance-per-day', JSON.stringify({
    day: day.value,
    balance: balance.value
  }))
})

onMounted(()=>{
  let localStore = JSON.parse(localStorage.getItem('finance-per-day'))
  if(localStore){
    if(localStore.day) day.value = localStore.day
    if(localStore.balance) balance.value = localStore.balance
  }

  let date = moment()
  daysInMonth = date.daysInMonth()
  nowDay = date.date()
})
</script>

<template>
  <header class="header">
    <img class="header__logo" src="./assets/logo-color.svg" alt="logo">
    <div class="header__data">
      <div class="form__group field">
        <input 
          type="number" 
          class="form__field" 
          placeholder="День місяця" 
          name="date" 
          id='date' 
          v-model="day"
          @change="onChangeDay"
        />
        <label for="date" class="form__label">День місяця</label>
      </div>
      <div class="form__group field">
        <input 
          type="number" 
          class="form__field" 
          placeholder="Баланс" 
          name="balance" 
          id='balance' 
          v-model="balance"
        />
        <label for="balance" class="form__label">Баланс</label>
      </div>
    </div>
  </header>

  <main class="main">
    <div class="main__result">{{perDay}}</div>
    <div class="main__subtext">&#8372; per day</div>
  </main>
  
  <footer class="footer">
    <div class="footer__text">Made with &hearts; by <a href="mailto:k.tuz.work@gmail.com" class="">k.tuz</a></div>
  </footer>
</template>
