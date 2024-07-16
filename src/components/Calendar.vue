<template>
  <div
    id="calendarContainer"
    style="border-radius: 10px;"
    :style="{backgroundColor: backgroundColor ? backgroundColor : '#f9f9f9', padding: contWidth < 300 ? '15px' : '20px'}"
  >
    <div
      style="position: relative; width: full; display: flex; flex-direction: row; justify-content: space-between; align-items: center; justify-items: center; padding-bottom: 20px;"
    >
      <button @click="changeMonth()" :style="ovuebuttonStyle()">
        <svg width="10" height="18" viewBox="0 0 10 18" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M0.178122 9.64776L8.84488 17.8139C9.09612 18.0495 9.51737 18.0633 9.78736 17.8445C10.0567 17.6251 10.0724 17.2564 9.82174 17.0202L1.57623 9.25026L9.82174 1.48037C10.0717 1.24412 10.0561 0.874753 9.78674 0.656006C9.65862 0.552257 9.49549 0.500383 9.33362 0.500383C9.15487 0.500383 8.97675 0.562882 8.84488 0.68663L0.178122 8.85277C-0.0593743 9.07714 -0.0593743 9.42338 0.178122 9.64776Z" fill="black"/>
        </svg>
      </button>
      <button :style="ovuebuttonStyle()" @click="showMonthSelection = !showMonthSelection">
        {{ currentMonth + ' ' + currentYear }}
      </button>
      <button @click="changeMonth(true)" :style="ovuebuttonStyle()" style="justify-content: end;">
        <svg width="10" height="18" viewBox="0 0 10 18" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M9.82166 8.35247L1.1549 0.186332C0.903654 -0.04929 0.48241 -0.0630398 0.212414 0.155707C-0.0569573 0.375079 -0.0725823 0.743824 0.178039 0.980071L8.42355 8.74996L0.178039 16.5199C-0.0719573 16.7561 -0.0563327 17.1255 0.213039 17.3442C0.341162 17.448 0.504285 17.4998 0.666158 17.4998C0.844905 17.4998 1.02303 17.4373 1.1549 17.3136L9.82166 9.14746C10.0592 8.92309 10.0592 8.57684 9.82166 8.35247Z" fill="black"/>
        </svg>
      </button>
      <div v-if="showMonthSelection" style="position: absolute; z-index: 100; width: 100%;display: flex; top:30px; font-size: 14px;">
        <div :style="{backgroundColor: backgroundColor ? backgroundColor : '#f9f9f9'}" style="margin: auto; border: 1px solid #A6A6A6; padding: 20px; border-radius: 5px;">
          <button
            @click="showYearModal()"
            style="font-weight: bold; margin: 0; border: none; background: none; cursor: pointer; outline: none;"
            :style="{fontSize: contWidth < 350 ? '14px' : '16px'}"
          >
            {{ currentYear }}
          </button>
          <div style="display: grid; grid-template-columns: repeat(2, min-content); row-gap: 20px; column-gap: 40px; margin-top: 10px;">
            <button
              @click="changeMonth(false, idx)"
              v-for="(month, idx) in months"
              :key="'month' + idx"
              style="margin: 0; border: none; background: none; cursor: pointer; outline: none;"
              :style="{
                fontWeight: idx === currentMonthNumb ? 'bold' : 'normal',
                fontSize: contWidth < 350 ? '14px' : '16px'
              }"
            >
              {{ month }}
            </button>
          </div>
        </div>
      </div>
    </div>
    <div style="justify-content: space-between; width: full; text-align: left; display: grid; grid-template-columns: repeat(7, min-content);">
      <p
        v-for="(day, idx) in days"
        :key="idx"
        :style="{fontSize: contWidth < 350 ? '14px' : '16px'}"
      >
        {{ day }}
      </p>
      <p
        v-for="(date, idx) in lastMonthRange"
        :key="'prevdate' + idx"
        style="text-align: center; color: #A6A6A6;"
        :style="{fontSize: contWidth < 350 ? '14px' : '16px'}"
      >
        {{ date }}
      </p>
      <p
        v-for="(date, idx) in lastDate"
        :key="'date' + idx"
        :style="styleDate(date)"
      >
        {{ date }}
    </p>
      <p
        v-for="(date, idx) in nextMonthRange"
        :key="'nextdate' + idx"
        style="text-align: center; color: #A6A6A6;"
        :style="{fontSize: contWidth < 350 ? '14px' : '16px'}"
      >
        {{ date }}
      </p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VuCalendar',
  props: {
    activeDate: Date,
    themeColor: String,
    backgroundColor: String
  },
  data () {
    return {
      today: new Date().setHours(0,0,0,0),
      currentDate: new Date(),
      selectedDate: this.activeDate ? this.activeDate : new Date(),
      currentMonth: '',
      currentMonthNumb: new Date().getMonth(),
      currentYear: new Date().getFullYear(),
      lastDate: 30,
      lastMonthRange: [],
      nextMonthRange: [],
      days: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'fri', 'Sat'],
      months: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Des'],
      showMonthSelection: false,
      showYearSelection: false,
      choosenYear: null,
      contWidth: null
    }
  },
  mounted () {
    this.getCurrentMonth()
    this.getLastDate()
    this.contWidth = document.getElementById('calendarContainer').offsetWidth
    if(this.contWidth < 350) {
      this.days = ['S', 'M', 'T', 'W', 'T', 'F', 'S']
    }
  },
  methods: {
    showYearModal () {
      this.showYearSelection = !this.showYearSelection
      this.showMonthSelection = !this.showMonthSelection
    },
    getCurrentMonth (chdate) {
      let date
      if (chdate) {
        date = new Date(chdate);
        this.currentYear = date.getFullYear()
        this.currentMonthNumb = date.getMonth()
      } else {
        date = new Date()
      }
      this.currentMonth = date.toLocaleString('default', { month: 'long' });
    },
    getLastDate (chdate) {
      // this.lastMonthRange = []
      const lastMonthArr = []
      const nextMonthArr = []
      let date
      if (chdate) {
        date = new Date(chdate)
      } else {
        date = new Date
      }
      const y = date.getFullYear(), m = date.getMonth()
      this.lastDate = new Date(y, m + 1, 0).getDate()
      const firstDay = new Date(y, m, 1).getDay()
      const lastDay = new Date(y, m + 1, 0).getDay()
      const range = 6 - lastDay

      if (firstDay > 0) {
        const lastMonthLastDate = m > 1 ? new Date(y, m, 0).getDate() : 31
        for (let x = 0; x < firstDay; x++) {
          lastMonthArr.unshift(lastMonthLastDate - x)
        }
      }
      if (range > 0) {
        for (let x = 1; x <= range; x++) {
          nextMonthArr.push(x)
        }
      }
      this.lastMonthRange = lastMonthArr
      this.nextMonthRange = nextMonthArr
    },
    changeMonth (next, month) {
      if (month || month === 0) {
        this.currentMonthNumb = month
      } else if (this.currentMonthNumb === 11 && next) {
        this.currentYear += 1
        this.currentMonthNumb = 0
      } else if (this.currentMonthNumb === 0 && !next) {
        this.currentYear -= 1
        this.currentMonthNumb = 11
      } else if (next) {
        this.currentMonthNumb += 1
      } else {
        this.currentMonthNumb -= 1
      }

      const date = new Date(this.currentYear, this.currentMonthNumb, 1)
      this.currentMonth = date.toLocaleString('default', { month: 'long' });
      this.getLastDate(date)
      this.showMonthSelection = false
    },
    styleDate (date) {
      const style = {}
      style['justify-content'] = 'center'
      style['text-align'] = 'center'
      if (this.contWidth < 350) {
        style['font-size'] = '14px'
      }
      const selecdate = new Date(this.currentYear, this.currentMonthNumb, date).setHours(0,0,0,0)
      if (selecdate === this.today) {
        style['font-weight'] = 'bold'
        style.color = this.themeColor ? this.themeColor : 'black'
        // const rgb = 
        // style['background-color'] = 'rgb(231,48,48,0.5)'
        // style['border-radius'] = '100%'
      }
      return style
    },
    ovuebuttonStyle () {
      return {
        border: 'none',
        background: 'none',
        cursor: 'pointer',
        outline: 'none',
        'font-size': '16px',
        'font-weight': 'bold',
        padding: 0
      }
    }
  }
}
</script>
