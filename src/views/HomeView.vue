<script setup>
import { ref, onMounted } from 'vue'

const goods = ref([])
const goodsFridge = ref([])
const goodsFreezer = ref([])

function countDaysLeft(dateString) {
  const endDate = new Date(dateString)
  const startDate = new Date()
  const diff = endDate - startDate

  const days = Math.ceil(diff / (1000 * 60 * 60 * 24))

  let className

  if (days == 0) {
    return { days: `Срок годности выходит сегодня!`, className: 'text-orange-400' }
  }

  if (days < 0) {
    return { days: `Срок годности вышел ${days * -1}  дней назад!`, className: 'text-red-400' }
  }

  if (days < 4) {
    className = 'text-orange-400'
  } else if (days < 7) {
    className = 'text-yellow-400'
  }

  return { days, className }
}

function countDaysLeftFreezer(dateString, type, idx) {
  const productTypes = {
    calf: '180',
    beef: '120',
    pork: '120',
    bird: '210',
    semifinished: '120',
    game: '180',
    bread: '180',
  }
  const endDate = new Date(dateString)
  const startDate = new Date()
  const diff = endDate - startDate

  const days = Math.ceil(diff / (1000 * 60 * 60 * 24)) + +productTypes[type]

  goodsFreezer.value[idx].days = days

  let className

  if (days == 0) {
    return { days: `Срок годности выходит сегодня!`, className: 'text-orange-400' }
  }

  if (days < 0) {
    return { days: `Срок годности вышел ${days * -1}  дней назад!`, className: 'text-red-400' }
  }

  if (days < 4) {
    className = 'text-orange-400'
  } else if (days < 7) {
    className = 'text-yellow-400'
  }

  return { days, className }
}

onMounted(async () => {
  try {
    const res = await fetch('/src/db/goods.json')
    if (!res.ok) {
      throw new Error(`HTTP error! status: ${res.status}`)
    }
    const data = await res.json()
    goods.value = data.filter((item) => !item.fridge && !item.freezer)
    goodsFridge.value = data.filter((item) => item.fridge)
    goodsFreezer.value = data.filter((item) => item.freezer)
    goods.value = [...goods.value].sort((a, b) => new Date(a.date) - new Date(b.date))
    goodsFridge.value = [...goodsFridge.value].sort((a, b) => new Date(a.date) - new Date(b.date))
  } catch (error) {
    console.error('Error fetching goods:', error)
  }
})
</script>

<template>
  <main class="flex flex-wrap justify-center">
    <h1 class="mt-7 mb-15 w-full capitalize text-center">Expiration date calender</h1>
    <div class="flex gap-10">
      <div>
        <h2 class="mb-5 w-full text-2xl text-center">Продовольствие</h2>
        <ul class="list-disc">
          <li v-for="item in goods" :key="item" class="p-2 capitalize">
            {{ item.name }} {{ item.amount ? ` { x${item.amount} }` : '' }}
            <span
              class="normal-case text-blue-100 font-medium"
              :class="countDaysLeft(item.date).className"
            >
              {{ `(${countDaysLeft(item.date).days})` }}
            </span>
          </li>
        </ul>
      </div>
      <div>
        <div class="mb-10">
          <h2 class="mb-5 w-full text-2xl text-center">Холодильник</h2>
          <ul class="list-disc">
            <li v-for="item in goodsFridge" :key="item" class="p-2 capitalize">
              {{ item.name }} {{ item.amount ? ` { x${item.amount} }` : '' }}
              <span
                class="normal-case text-blue-100 font-medium"
                :class="countDaysLeft(item.date).className"
              >
                {{ `(${countDaysLeft(item.date).days})` }}
              </span>
            </li>
          </ul>
        </div>
        <div>
          <h2 class="mb-5 w-full text-2xl text-center">Морозильник</h2>
          <ul class="list-disc">
            <li v-for="(item, i) in goodsFreezer" :key="item" class="p-2 capitalize">
              {{ item.freezer ? `${item.name} [❄️]` : '' }}
              {{ item.amount ? ` { x${item.amount} }` : '' }}
              <span
                class="normal-case text-blue-100 font-medium"
                :class="countDaysLeftFreezer(item.date, item.type, i).className"
              >
                {{ `(${countDaysLeftFreezer(item.date, item.type, i).days})` }}
              </span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </main>
</template>
