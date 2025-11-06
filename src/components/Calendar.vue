<script setup>
import Week from './Week.vue';
import WeekHeader from './WeekHeader.vue';
</script>

<template>
    <div style="width: fit-content; display: flex; flex-direction: column; gap: 0.1rem">
        <div style="display: flex; gap: 0.1rem; justify-content: space-between; padding: 0.1rem;">
            <select v-model="locale" style="width: 7rem;">
                <option v-for="language in languages" :value="language.id">{{ language.label }}</option>
            </select>
            <input placeholder="yyyy-mm-dd" v-model="searchDateString" @change="parseDate()" style="width: 10rem;"></input>
        </div>
        <div style="display: flex; gap: 0.1rem; justify-content: space-between; padding: 0.1rem;">
            <button @click="decreaseMonth"><i class="arrow left"></i></button>
            <div>
                {{ monthLabel }} {{ year }}
            </div>
            <button @click="increaseMonth"><i class="arrow right"></i></button>
        </div>
        <div style="display: flex; flex-direction: column; gap: 0.1rem;">
            <WeekHeader :days="weekDays"/>
            <Week v-for="week in weeks" :days="week" @select-day="(day) => changeCurrentDate(day)"/>
        </div>   
    </div>    
</template>

<script>
export default {
  name: 'Calendar',
  data() {
    return {
        locale: 'ru-RU',
        month: null,
        year: null,
        currentDate: null,
        searchDateString: null,
        languages: [
            {id:'en-EN', label:'English'},
            {id:'ru-RU', label:'Русский'}
        ]
    }
  },
  computed: {
    monthLabel: {
        get: function() {
            if (this.month != null) {
                const date = new Date(2000, this.month, 1); 
                return date.toLocaleString(this.locale, { month: 'long' }).toUpperCase();
            } else 
                return null
        }
    },
    weekDays: {
        get() {
            const monday = new Date(2023, 0, 2);
            const res = [];
            
            // Получаем названия всех дней недели
            for (let i = 0; i < 7; i++) {
                const date = new Date(monday);
                date.setDate(monday.getDate() + i);
                
                const name = date.toLocaleDateString(this.locale, { weekday: 'short' });
                res.push(name);
            }
            return res;
        }
    },
    weeks: {
        get: function() {
            if (this.year == null)
                return [];
            const weeks = [];

            const firstDay = new Date(this.year, this.month, 1); // первый день месяца
            const lastDay = new Date(this.year, this.month + 1, 0); // последний день месяца

            const startDate = new Date(firstDay);
            const dayOfWeek = firstDay.getDay(); 

            const mondayOffset = dayOfWeek === 0 ? -6 : 1 - dayOfWeek;
            startDate.setDate(firstDay.getDate() + mondayOffset); // определяем первый день в неделе
            
            let currentDate = new Date(startDate); // заводим счетчик

            while (
                currentDate <= lastDay || 
                new Date(currentDate.getTime() + 6 * 24 * 60 * 60 * 1000) <= lastDay || 
                weeks.length === 0
            ) {
                
                const week = [];

                for (let i = 0; i < 7; i++) {
                    const dayDate = new Date(currentDate);
                    const dayInfo = {
                        date: dayDate,
                        day: dayDate.getDate(),
                        isCurrentMonth: dayDate.getMonth() === this.month && dayDate.getFullYear() === this.year,
                        isCurrentDay: dayDate.getDate() === this.currentDate.getDate() && 
                                      dayDate.getMonth() === this.currentDate.getMonth() && 
                                      dayDate.getFullYear() === this.currentDate.getFullYear(),
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
    },
    increaseMonth() {
        if (this.month == 11) {
            this.month = 0;
            this.year += 1;
        } else {
            this.month += 1;
        }
    },
    changeCurrentDate(date) {
        // console.log('month', date)
        this.currentDate = date;
        this.month = this.currentDate.getMonth();
        this.year = this.currentDate.getFullYear();
    },
    parseDate() {
        let searchDate = null;
        try {
            searchDate = new Date(this.searchDateString);
            this.searchDateString = null;
            this.changeCurrentDate(searchDate);
        } catch (error) {
            console.log('Error! Wrong format date!')
        }
    }
  },
  mounted() {
    this.currentDate = new Date();
    this.month = this.currentDate.getMonth();
    this.year = this.currentDate.getFullYear();
    // console.log(this.currentDate)
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