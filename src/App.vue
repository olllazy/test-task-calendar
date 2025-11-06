<script setup>
import Calendar from './components/Calendar.vue';
</script>

<template>
  <h1>Календарь</h1>
  <Calendar current-date="2024-12-20" @select-day="(day) => console.log(day)"/>
</template>

<script>
</script>

<style scoped>
</style>
