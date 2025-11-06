<script setup>
import DayHeader from './DayHeader.vue';
</script>

<template>
    <div style="display: flex; gap: 0.1rem;">
        <DayHeader v-for="day in days" :day="day"/>
    </div>   
</template>

<script>
export default {
  name: 'WeekHeader',
  props: {
    days: Array,
  },
  data() {
    return {}
  }
};
</script>