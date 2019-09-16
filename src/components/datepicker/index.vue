<template>
  <transition name="picker-fade">
    <div class="picker-wrap" @click="onCancel" @touchmove.prevent="onStopProgation" v-if="visible">
      <div class="picker" @click.stop="onStopProgation">
        <div class="header">
          <div class="year" @click="onOpenList('year')">{{currentYear}}{{ lang.name === 'cn' ? lang.year : ''}}</div>
          <div class="month" @click="onOpenList('month')">{{getCurrentMonth(currentMonth)}}{{lang.month}} - {{getCurrentDay(currentDay)}}{{lang.day}}</div>
        </div>
        <div class="container" v-if="!isShow">
          <div class="navigation">
            <span class="prev" @click="onPrev"></span>
            <div>{{formaterMonth(currentMonth)}} {{currentYear}}{{lang.name === 'cn' ? lang.year : ''}}</div>
            <span class="next" @click="onNext"></span>
          </div>
          <ul class="week">
            <li v-for="(item, index) in weeks" :key="index">{{item}}</li>
          </ul>
          <div class="day" @click="onSelect($event)" ref="dayRef">
            <template v-if="spaceDays > 0">
              <div v-for="(item, index) in spaceDays" :key="index"></div>
            </template>
            <div :class="{cell: true, active: item === currentDateRemark.getDate() && currentDateRemark.getMonth() === currentMonth && currentDateRemark.getFullYear() === currentYear}" v-for="(item, index) in days" :key="index + 's'">
            <!-- <div :class="{cell: true, active: item === currentDay}" v-for="(item, index) in days" :key="index + 's'"> -->
              <span>{{item * 1 < 10 ? '0' + item : item}}</span>
              <i></i>
            </div>
          </div>
          <div class="footer">
            <button @click="onCancel">{{cancelText || lang.cancel}}</button>
            <button @click="onConfirm">{{confirmText || lang.ok}}</button>
          </div>
        </div>
        <div class="months-wrap" v-if="isShow" ref="monthsRef">
          <ul class="months" @click="onSelectList($event)" @touchmove.stop="onStopProgation">
            <li v-for="(item, index) in list" :class="{active: (selectedType === 'month' && currentMonth === index) || (selectedType === 'year' && currentYear === item)}" :year="item" :id="index" :key="index">{{item}} {{selectedType === 'year' && lang.name === 'cn' ? lang.year : ''}}</li>
          </ul>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
/* eslint-disable */

export default {
  name: 'Picker',
  props: ['visible', 'startDate', 'confirmText', 'cancelText', 'language'],
  watch: {
    visible (val) {
      let body = document.body
      if (val) {
        body.style.position = 'fixed'
        this.initCreated()
      } else {
        body.style.position = 'static'
      }
    }
  },
  data () {
    return {
      lang: {},
      weeks: [],
      days: [],
      spaceDays: [],
      currentDate: null,
      currentYear: 0,
      currentMonth: 0,
      currentDay: 0,
      currentDateRemark: null,
      list: [],
      isShow: false,
      selectedType: ''
    }
  },
  created () {
    this.initCreated()

    // Select the current language, default to cn
    let currentLanguage = this.language
    if (typeof this.language === 'undefined') {
      currentLanguage = 'cn'
    }

    this.lang = require('./../../lang/' + currentLanguage + '.js')
    this.weeks = this.lang.weeks
  },
  methods: {
    initCreated () {
      if (this.startDate) {
        this.currentDate = new Date(this.startDate)
        this.currentDateRemark = new Date(this.startDate)
      } else {
        this.currentDate = new Date()
        this.currentDateRemark = new Date()
      }
      this.currentYear = this.currentDate.getFullYear()
      this.currentMonth = this.currentDate.getMonth()
      this.currentDay = this.currentDate.getDate()
      // this.currentDateRemark = new Date()
      let { currentYear, currentMonth } = this
      this.initDate(currentYear, currentMonth)
    },
    initDate (currentYear, currentMonth) {
      let spaceDays = this.getWeek(currentYear, currentMonth) - 1
      let days = this.getDay(currentYear, currentMonth)
      this.spaceDays = spaceDays
      this.days = days
    },
    getCurrentMonth (currentMonth) {
      let month = currentMonth + 1
      month = month < 10 ? '0' + month : month
      return month
    },
    getCurrentDay (currentDay) {
      let day = currentDay < 10 ? '0' + currentDay : currentDay
      return day
    },
    onStopProgation () {
    },
    onSelectList (evt) {
      let target = evt.target
      let node = this.$refs.monthsRef.querySelector('.active')
      if (node) {
        node.classList.remove('active')
      }
      target.classList.add('active')
      if (this.selectedType === 'month') {
        let month = target.id * 1
        this.currentMonth = month
      } else {
        let year = target.getAttribute('year') * 1
        this.currentYear = year
      }
      let timer = setTimeout(() => {
        this.isShow = false
        this.initDate(this.currentYear, this.currentMonth)
        clearTimeout(timer)
      }, 400)
    },
    onOpenList (type) {
      if (type === 'month') {
        this.list = this.lang.months
      } else {
        let years = []
        let yearRange = this.currentDateRemark.getFullYear()
        for (let i = 1909; i <= yearRange; i++) {
          years.push(i)
        }
        this.list = years
      }
      this.selectedType = type
      this.isShow = true
      this.$nextTick(() => {
        let monthsRef = this.$refs.monthsRef
        let node = monthsRef.querySelector('.active')
        monthsRef.scrollTop = node.offsetTop - 200
      })
    },
    getWeek (year, month) {
      return new Date(year, month, 1).getDay()
    },
    getDay (year, month) {
      return new Date(year, month + 1, 0).getDate()
    },
    initNode () {
      let node = this.$refs.dayRef.querySelector('.active')
      if (node) {
        node.classList.remove('active')
      }
    },
    onPrev () {
      this.initNode()
      this.currentMonth -= 1
      let { currentMonth } = this
      if (currentMonth < 0) {
        this.currentMonth = 11 // 12月
        this.currentYear -= 1
      }
      this.initDate(this.currentYear, this.currentMonth)
    },
    onNext () {
      this.initNode()
      this.currentMonth += 1
      let { currentMonth } = this
      if (currentMonth > 11) {
        this.currentMonth = 0
        this.currentYear += 1
      }
      this.initDate(this.currentYear, this.currentMonth)
    },
    onSelect (evt) {
      let target = evt.target
      let type = target.tagName.toLowerCase()
      if (type === 'span' || type === 'i') {
        target = target.parentNode
      }
      if (!target.classList.contains('cell')) {
        return false
      }
      let node = this.$refs.dayRef.querySelector('.active')
      if (node) {
        node.classList.remove('active')
      }
      target.classList.add('active')
      this.currentDay = target.textContent * 1
    },
    onConfirm () {
      let year = this.currentYear
      let month = (this.currentMonth + 1)
      let day = this.currentDay
      month = month < 10 ? '0' + month : month
      day = day < 10 ? '0' + day : day
      let date = year + '-' + month + '-' + day
      this.$emit('change', date)
      this.$emit('update:visible', false)
      this.$emit('update:startDate', date)
      this.isShow = false
    },
    onCancel () {
      this.$emit('update:visible', false)
      this.$emit('cancel')
      this.isShow = false
    },
    formaterMonth (val) {
      return this.lang.months[val]
    }
  }
}
</script>

<style scoped>
ul{
  margin: 0;
  padding: 0;
  list-style: none;
}
.picker-wrap{
  width: 100%;
  height: 100%;
  position: fixed;
  left: 0;
  top: 0;
  z-index: 100;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  color: #444;
  font-size: 16px;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", sans-serif;
  -webkit-tap-highlight-color: transparent;
}
.picker{
  width: 90vw;
  min-height: 67vh;
  max-height: 75vh;
  background: #fff;
  border-radius: 3px;
  display: flex;
  flex-direction: column;
}
.header{
  height: 48px;
  background: #29d1b4;
  padding: 18px 30px;
}
.header .year{
  color: #fefefe;
  font-weight: bold;
  font-size: 18px;
  cursor: pointer;
}
.header .month{
  color: #fff;
  font-size: 28px;
  line-height: 1;
  cursor: pointer;
}
.navigation{
  margin: 13px 20px;
  display: flex;
}
.navigation span{
  width: 10%;
  display: flex;
  align-items: center;
}
.navigation span:after{
  width: 9px;
  height: 9px;
  border: 2px solid #666;
  display: block;
  border-left: none;
  border-bottom: none;
  display: block;
  content: "";
}
.navigation .prev:after{
  transform: rotate(-135deg);
}
.navigation .next:after{
  transform: rotate(45deg);
}
.navigation .prev{
  padding-left: 5%;
}
.navigation .next{
  justify-content: flex-end;
  padding-right: 5%;
}
.navigation div{
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 18px;
}
.week{
  display: flex;
  font-size: 15px;
  padding: 0 20px;
  font-weight: 500;
  text-align: center;
}
.week li{
  flex: 1;
}
.day{
  padding: 10px 20px;
  display: flex;
  flex-wrap: wrap;
}
.day div{
  width: calc(100% / 7);
  height: 36px;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}
.day div span{
  position: relative;
  z-index: 2;
}
.day div i{
  opacity: 0;
  transition: opacity 0.3s;
}
.day div.active i{
  width: 36px;
  height: 36px;
  background: #29d1b4;
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -18px;
  margin-top: -18px;
  border-radius: 100%;
  opacity: 1;
}
.day div.active span{
  color: #fff;
}
.footer{
  display: flex;
  justify-content: flex-end;
  padding: 0 20px;
}
.footer button{
  padding: 10px 20px 12px;
  font-size: 16px;
  font-weight: bold;
  color: #29d1b4;
  -webkit-appearance: none;
  background: none;
  border: none;
}
.months-wrap{
  flex: 1;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}
.months{
  padding: 20px 20px;
  text-align: center;
}
.months li{
  height: 35px;
  line-height: 35px;
  font-weight: bold;
  transition: all 0.1s;
}
.months li.active{
  font-size: 24px;
  color: #29d1b4;
}
.picker-fade-enter-active, .picker-fade-leave-active{
  transition: all .5s;
}
.picker-fade-enter, .picker-fade-leave-to{
  opacity: 0;
  transform: translateY(-20px);
}
</style>
