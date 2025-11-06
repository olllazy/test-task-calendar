<script setup>
import Week from './Week.vue';
import WeekHeader from './WeekHeader.vue';
</script>

<template>
    <div style="display: flex;">
        {{ currentLanguage }} {{ month }} {{ year }}
    </div>
    <div style="width: fit-content; display: flex; flex-direction: column; gap: 0.1rem">
        <div style="display: flex; gap: 0.1rem; justify-content: space-between; padding: 0.1rem;">
            <select v-model="currentLanguage" style="width: 7rem;">
                <option v-for="language in languages" :value="language.id">{{ language.label }}</option>
            </select>
            <input v-model="searchDate" style="width: 10rem;"></input>
        </div>
        <div style="display: flex; gap: 0.1rem; justify-content: space-between; padding: 0.1rem;">
            <button @click="decreaseMonth"><i class="arrow left"></i></button>
            <div>
                {{ monthLabel }} {{ year }}
            </div>
            <button @click="increaseMonth"><i class="arrow right"></i></button>
        </div>
        <div style="display: flex; flex-direction: column; gap: 0.1rem;">
            <WeekHeader :days="days"/>
            <Week v-for="week in weeks" :days="week"/>
        </div>   
    </div>    
</template>

<script>
export default {
  name: 'Calendar',
  data() {
    return {
        currentLanguage: 'ru-RU',
        month: null,
        year: null,
        currentDate: null,
        searchDate: null,

        // weeks: [
        //     [1,2,3,4,5,6,7],
        //     [8,9,10,11,12,13,14],
        //     [15,16,17,18,19,20,21],
        //     [22,23,24,25,26,27,28],
        //     [29,30,31]
        // ],
        days: ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'],
        languages: [{id:'en-EN', label:'English'},{id:'ru-RU', label:'Русский'}]
    }
  },
  computed: {
    monthLabel: {
        get: function() {
            if (this.month != null) {
                const date = new Date(2000, this.month, 1); 
                return date.toLocaleString(this.currentLanguage, { month: 'long' }).toUpperCase();
            } else 
                return null
        }
    },
    weeks: {
        get: function() {
            if (this.year == null)
                return [];
            const weeks = [];

            const firstDay = new Date(this.year, this.month, 1);
            const lastDay = new Date(this.year, this.month + 1, 0);

            const startDate = new Date(firstDay);
            const dayOfWeek = firstDay.getDay(); 

            const mondayOffset = dayOfWeek === 0 ? -6 : 1 - dayOfWeek;
            startDate.setDate(firstDay.getDate() + mondayOffset);
            
            let currentDate = new Date(startDate);

            while (currentDate <= lastDay || 
                new Date(currentDate.getTime() + 6 * 24 * 60 * 60 * 1000) <= lastDay ||
                weeks.length === 0) {
                
                const week = [];

                for (let i = 0; i < 7; i++) {
                    const dayDate = new Date(currentDate);
                    const dayInfo = {
                        date: dayDate,
                        day: dayDate.getDate(),
                        isCurrentMonth: dayDate.getMonth() === this.month && dayDate.getFullYear() === this.year,
                        dayOfWeek: i 
                    };
                    
                    week.push(dayInfo);
                    currentDate.setDate(currentDate.getDate() + 1);
                }
                
                if (week.some(day => day.isCurrentMonth)) {
                    weeks.push(week);
                }
            }

            return weeks;
        }
    }
  },
  methods: {
    decreaseMonth() {
        if (this.month == 0) {
            this.month = 11;
            this.year -= 1;
        } else {
            this.month -= 1;
        }
        console.log(this.weeks);
    },
    increaseMonth() {
        if (this.month == 11) {
            this.month = 0;
            this.year += 1;
        } else {
            this.month += 1;
        }
        console.log(this.weeks);
    }
  },
  mounted() {
    this.currentDate = new Date();
    this.month = this.currentDate.getMonth();
    this.year = this.currentDate.getFullYear();
    console.log(this.currentDate)
  }
};
</script>

<style scoped>
button {
    background-color: transparent;
    border: none;
    outline: none;
}

.arrow {
  border: solid black;
  border-width: 0 3px 3px 0;
  display: inline-block;
  padding: 3px;
}

.right {
  transform: rotate(-45deg);
  -webkit-transform: rotate(-45deg);
}

.left {
  transform: rotate(135deg);
  -webkit-transform: rotate(135deg);
}

.up {
  transform: rotate(-135deg);
  -webkit-transform: rotate(-135deg);
}

.down {
  transform: rotate(45deg);
  -webkit-transform: rotate(45deg);
}
</style>