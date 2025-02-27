<script setup>
import { ref, onMounted } from 'vue'

const goods = ref([])

function countDaysLeft(dateString) {
  const endDate = new Date(dateString)
  const startDate = new Date()
  const diff = endDate - startDate

  const days = Math.ceil(diff / (1000 * 60 * 60 * 24)) // Calculate days and round up

  let className

  if (diff < 0) {
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
    goods.value = await res.json()
    goods.value = [...goods.value].sort((a, b) => new Date(a.date) - new Date(b.date))
  } catch (error) {
    console.error('Error fetching goods:', error)
  }
})

console.log('goods value outside mount:', goods.value)
</script>

<template>
  <main class="flex flex-wrap justify-center">
    <h1 class="mt-7 mb-15 w-full capitalize text-center">Expiration date calender</h1>
    <ul class="list-disc">
      <li v-for="item in goods" :key="item" class="p-2 capitalize">
        {{ item.name }}
        <span
          class="normal-case text-blue-100 font-medium"
          :class="countDaysLeft(item.date).className"
        >
          {{ `(${countDaysLeft(item.date).days})` }}
        </span>
      </li>
    </ul>
  </main>
</template>
