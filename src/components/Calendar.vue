<script setup>
import Week from './Week.vue';
import WeekHeader from './WeekHeader.vue';
</script>

<template>
    <div class="calendar-container">
        <div class="calendar-item" style="justify-content: space-between;">
            <select v-model="locale" style="width: 7rem;">
                <option v-for="language in languages" :value="language.id">{{ language.label }}</option>
            </select>
            <input placeholder="yyyy-mm-dd" v-model="searchDateString" @change="parseDate()" style="width: 10rem;"></input>
        </div>
        <div class="calendar-item" style="justify-content: space-between; margin-top: 0.5rem;">
            <button @click="decreaseMonth"><i class="arrow left"></i></button>
            <div>
                {{ monthLabel }} {{ year }}
            </div>
            <button @click="increaseMonth"><i class="arrow right"></i></button>
        </div>
        <div class="calendar-item" style="flex-direction: column;">
            <WeekHeader :days="weekDays"/>
            <Week v-for="week in weeks" :days="week" @select-day="(day) => changeCurrentDate(day)"/>
        </div>   
    </div>    
</template>

<script>
export default {
  name: 'Calendar',
  props: {
    currentDate: {
        type: String,
        required: false,
    }
  },
  data() {
    return {
        locale: 'ru-RU',
        month: null,
        year: null,
        _currentDate: null,
        searchDateString: null,
        languages: [
            {id:'en-EN', label:'English'},
            {id:'ru-RU', label:'Русский'},
            {id: 'de-DE', label: 'Deutsch'}
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
            
            let _currentDate = new Date(startDate); // заводим счетчик

            while (
                _currentDate <= lastDay || 
                new Date(_currentDate.getTime() + 6 * 24 * 60 * 60 * 1000) <= lastDay || 
                weeks.length === 0
            ) {
                
                const week = [];

                for (let i = 0; i < 7; i++) {
                    const dayDate = new Date(_currentDate);
                    const dayInfo = {
                        date: dayDate,
                        day: dayDate.getDate(),
                        isCurrentMonth: dayDate.getMonth() === this.month && dayDate.getFullYear() === this.year,
                        isCurrentDay: dayDate.getDate() === this._currentDate.getDate() && 
                                      dayDate.getMonth() === this._currentDate.getMonth() && 
                                      dayDate.getFullYear() === this._currentDate.getFullYear(),
                        dayOfWeek: i 
                    };
                    
                    week.push(dayInfo);
                    _currentDate.setDate(_currentDate.getDate() + 1);
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
        this._currentDate = date;
        this.month = this._currentDate.getMonth();
        this.year = this._currentDate.getFullYear();
        let tzoffset = this._currentDate.getTimezoneOffset() * 60000; //
        let localISOTime = (new Date(this._currentDate - tzoffset)).toISOString().slice(0, -1);
        this.$emit('selectDay', localISOTime.split('T')[0]);
    },
    parseDate() {
        const regex = /^\d{4}\-(0?[1-9]|1[012])\-(0?[1-9]|[12][0-9]|3[01])$/;
        if (regex.test(this.searchDateString) && this.searchDateString.length == 10)
        {
            const searchDate = new Date(this.searchDateString);
            this.searchDateString = null;
            this.changeCurrentDate(searchDate);
        } else {
            console.log('Error! Wrong format date!')
        }
    }
  },
  mounted() {
    const regex = /^\d{4}\-(0?[1-9]|1[012])\-(0?[1-9]|[12][0-9]|3[01])$/;
    if (regex.test(this.currentDate) && this.currentDate.length == 10) {
        this._currentDate = new Date(this.currentDate);
    } else {
        this._currentDate = new Date();
    }
    this.month = this._currentDate.getMonth();
    this.year = this._currentDate.getFullYear();
    // console.log(this._currentDate)
  }
};
</script>

<style scoped>
.calendar-container {
    width: fit-content; 
    display: flex; 
    flex-direction: column; 
    gap: 0.2rem;
    padding: 1rem;
    border: 0.05rem solid lightgray;
    border-radius: 0.5rem;
}
.calendar-item {
    display: flex; 
    gap: 0.1rem;  
    padding: 0.1rem;
}
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