<script setup>
import Day from './Day.vue';
</script>

<template>
    <div style="display: flex; gap: 0.1rem;">
        <Day v-for="day in days" :day="day"/>
    </div>   
</template>

<script>
export default {
  name: 'Week',
  props: {
    days: Array,
  },
  data() {
    return {}
  }
};
</script>