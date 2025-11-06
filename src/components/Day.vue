<template>
    <div :class="computedClass" @click="selectDay">
        {{ (day)?day.day:'' }}
    </div>
</template>

<script>
export default {
  name: 'Day',
  props: {
    day: Object,
  },
  data() {
    return {
        checked: false,
    }
  },
  methods: {
    selectDay() {
        console.log('day', this.day.date)
        this.$emit('selectDay', this.day.date)      
    }
  },
  computed: {
    computedClass: {
        get: function() {
            let res = 'day-container'
            if (this.day.isCurrentDay) {
                res += ' current current-day'
                return res;
            }
            if (this.day.isCurrentMonth) {
                res += ' current current-month'
            } else {
                res += ' not-current'
            }
            return res
        }
    }
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
    .not-current {
        background-color: lightgrey;
    }
    .current-day {
        background-color: skyblue;
    }
    .current:hover {
        border-color:skyblue;
    }
    .current:active  {
        border-color: plum;
    }
</style>