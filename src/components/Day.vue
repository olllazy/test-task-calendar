<template>
    <div :class="computedClass" @click="dayClick">
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
    dayClick() {
        if (this.day.isCurrentMonth) {
            this.checked = !this.checked;
            console.log(this.day.date);
        }        
    }
  },
  computed: {
    computedClass: {
        get: function() {
            let res = 'day-container'
            if (this.day.isCurrentMonth) {
                res += ' current-year'
            } else {
                res += ' not-current-year'
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
    .not-current-year {
        background-color: lightgrey;
    }
    .current-year {
        background-color: transparent;
    }
    .current-year:hover {
        border-color:skyblue;
    }
    .current-year:active  {
        border-color: plum;
    }
</style>