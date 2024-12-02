<script setup lang="ts">
import file from '@/assets/input.txt'
import { ref } from 'vue'

import reports from '@/assets/reports.txt'

const fileContent = ref()
const leftList = ref([])
const rightList = ref([])
const totalDistance = ref(0)
const similarity = ref(0)

const reportsList = ref([])
const reportContent = ref()
const amountSafeReports = ref(0)

const handleFile = async () => {
  const res = await fetch(file)
  if (res.ok) {
    fileContent.value = await res.text()
    let pairsNotCorrect = fileContent.value.split('\n')
    pairsNotCorrect = pairsNotCorrect.slice(0, -1)
    for (let i = 0; i < pairsNotCorrect.length; i++) {
      const singles = pairsNotCorrect[i].split('   ')
      const nums = singles.map((str: string) => {
        return parseInt(str)
      })
      // eslint-disable-next-line @typescript-eslint/ban-ts-comment
      //@ts-expect-error
      leftList.value.push(nums[0])
      // eslint-disable-next-line @typescript-eslint/ban-ts-comment
      //@ts-expect-error
      rightList.value.push(nums[1])
    }
    leftList.value.sort()
    rightList.value.sort()
    for (let i = 0; i < leftList.value.length; i++) {
      totalDistance.value += Math.abs(leftList.value[i] - rightList.value[i])
      const amountRight = ref(0)
      for (let j = 0; j < rightList.value.length; j++) {
        if (leftList.value[i] == rightList.value[j]) {
          amountRight.value++
          //console.log(rightList.value[j] + ' (' + amountRight.value + ')')
        }
      }
      similarity.value += leftList.value[i] * amountRight.value
    }
  }
}

const handleReport = async () => {
  const res = await fetch(reports)
  if (res.ok) {
    reportContent.value = await res.text()
    const reportsSplitUp = reportContent.value.split('\n')
    reportsSplitUp.forEach((rep: string) => {
      const reportArrayString = rep.split(' ')
      const reportArray = reportArrayString.map((lev: string) => {
        return parseInt(lev)
      })
      reportsList.value.push(reportArray)
    })
    reportsList.value = reportsList.value.slice(0, -1)

    reportsList.value.forEach((rep: number[]) => {
      let wrong = false
      const ordered = rep.slice().sort((a,b) => a-b)
      const orderedReverse = rep.slice().sort((a,b) => b-a)

      if (rep.toString() !== ordered.toString() && rep.toString() !== orderedReverse.toString()) {
        wrong = true
      }

      for (let i = 1; i < rep.length; i++) {
        if (Math.abs(rep[i - 1] - rep[i]) < 1 || Math.abs(rep[i - 1] - rep[i]) > 3) {
          wrong = true
          break
        }
      }

      if (!wrong) {
        amountSafeReports.value++
        console.log(rep + '\n' + ordered + '\n' + orderedReverse)
      }
    })
  }
}
handleFile()
handleReport()
</script>

<template>
  <main>
    <h1>Advent of Code 2024</h1>
    <h2>Day 1</h2>
    <p>The total distance between all paired points is {{ totalDistance }}</p>
    <p>The similarity score is {{ similarity }}</p>
    <br />
    <h2>Day 2</h2>
    <p>{{ amountSafeReports }}</p>
  </main>
</template>

<style scoped></style>
