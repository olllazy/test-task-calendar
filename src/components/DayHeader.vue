<template>
    <div class="day-container">
        {{ day }}
    </div>
</template>

<script>
export default {
  name: 'DayHeader',
  props: {
    day: String,
  },
  data() {
    return {
        checked: false,
    }
  },
  methods: {
  }
};
</script>

<style scoped>
    .day-container {
        width: 2.5rem;
        height: 2.5rem;
        text-align: center;
        align-content: center;
        border: 0.1rem solid transparent;
    }
</style>