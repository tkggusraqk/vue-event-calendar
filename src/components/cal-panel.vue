<template>
    <div class="cal-wrapper">
        <div class="cal-header">
            <div class="l" @click="preMonth">
                <div :class="arrowBoth?'arrow-left icon':''">&nbsp</div>
            </div>
            <div class="title">
                <span v-if="!arrowBoth" @click="preMonth" class="arrow-left icon">&nbsp</span>
                {{curYearMonth}}
                <span v-if="!arrowBoth" @click="nextMonth" class="arrow-right icon">&nbsp</span>
            </div>
            <div class="r" @click="nextMonth">
                <div :class="arrowBoth?'arrow-right icon':''">&nbsp</div>
            </div>
        </div>
        <div class="cal-body">
            <div class="weeks">
        <span
                v-for="(dayName, dayIndex) in i18n[calendar.options.locale].dayNames"
                class="item"
                :key="dayIndex"
        >
          {{i18n[calendar.options.locale].dayNames[(dayIndex + calendar.options.weekStartOn) % 7]}}
        </span>
            </div>
            <div class="dates">
                <div class="row" v-for="(item,row) in  Math.ceil(dayList.length / 7)"
                     v-show="!isArrowUp || (isArrowUp && row==curDayRow)">
                    <div v-for="(date,index) in dayList.slice(row*7,row*7+7)" class="item" :key="date.date">
                        <p class="date-num"
                           @click="handleChangeCurday(date)"
                           :style="{color:  date.date == selectedDay ? '#fff' :(today == date.date?customColor:'inherit'),fontWeight:today == date.date?'bold':''}">
                            {{date.status ? date.date.split('/')[2] : '&nbsp'}}</p>
                        <span v-if="date.title && date.status"
                              :style="{backgroundColor:date.date == selectedDay?'white':''}"
                              class="is-today"></span>
                        <span class="is-event"
                              :style="{backgroundColor: date.date == selectedDay && date.status ? customColor : 'inherit'}"></span>
                    </div>
                </div>
            </div>
        </div>
        <div class="cal-footer" v-show="showCollapse">
            <div class="container">
                <div class="arrow" :class="!isArrowUp?'up':'down'" @click="arrowHandler">
                    <span class="icon"></span>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import i18n from '../i18n.js'
    import {dateTimeFormatter, isEqualDateStr} from '../tools.js'

    const inBrowser = typeof window !== 'undefined'
    export default {
        name: 'cal-panel',
        data() {
            return {
                i18n,
                isArrowUp: false,
                curDayRow: -1
            }
        },
        props: {
            events: {
                type: Array,
                required: true
            },
            calendar: {
                type: Object,
                required: true
            },
            selectedDay: {
                type: String,
                required: false
            },
            arrowBoth: {
                type: Boolean,
                default: false
            },
            showCollapse: {
                type: Boolean,
                default: true
            }
        },
        computed: {
            dayList() {
                let firstDay = new Date(this.calendar.params.curYear, this.calendar.params.curMonth, 1)
                let dayOfWeek = firstDay.getDay()
                // 根据当前日期计算偏移量 // Calculate the offset based on the current date
                if (this.calendar.options.weekStartOn > dayOfWeek) {
                    dayOfWeek = dayOfWeek - this.calendar.options.weekStartOn + 7
                } else if (this.calendar.options.weekStartOn < dayOfWeek) {
                    dayOfWeek = dayOfWeek - this.calendar.options.weekStartOn
                }

                let startDate = new Date(firstDay)
                startDate.setDate(firstDay.getDate() - dayOfWeek)

                let item, status, tempArr = [], tempItem
                let nullStartIndex = -1
                let nullEndIndex = -1
                for (let i = 0; i < 42; i++) {
                    item = new Date(startDate);
                    item.setDate(startDate.getDate() + i);

                    if (this.calendar.params.curMonth === item.getMonth()) {
                        status = 1
                    } else {
                        status = 0
                    }

                    tempItem = {
                        date: `${item.getFullYear()}/${item.getMonth() + 1}/${item.getDate()}`,
                        status: status,
                        customClass: []
                    }
                    if (this.curDayRow === -1 && tempItem.date === this.today) {
                        this.curDayRow = tempItem.date === this.today ? Math.floor(i / 6) : -1
                    }
                    this.events.forEach((event) => {
                        if (isEqualDateStr(event.date, tempItem.date)) {
                            tempItem.title = event.title
                            tempItem.desc = event.desc || ''
                            if (event.customClass) tempItem.customClass.push(event.customClass)
                        }
                    })
                    tempArr.push(tempItem)
                }
                //删除非当月空白行&解决选中后，回到上月默认选中空白项
                let allNull = true
                for (let i = 36; i < tempArr.length; i++) {
                    if (tempArr[i].status) {
                        allNull = false
                    }
                }
                if (allNull) {
                    return tempArr.splice(0, 35)
                }
                console.log('daylist')
                return tempArr
            },
            today() {
                let dateObj = new Date()
                return `${dateObj.getFullYear()}/${dateObj.getMonth() + 1}/${dateObj.getDate()}`
            },
            curYearMonth() {
                let tempDate = Date.parse(new Date(`${this.calendar.params.curYear}/${this.calendar.params.curMonth + 1}/01`))
                return dateTimeFormatter(tempDate, this.i18n[this.calendar.options.locale].format)
            },
            customColor() {
                return this.calendar.options.color
            }
        },
        methods: {
            nextMonth() {
                this.$EventCalendar.nextMonth()
                this.$emit('month-changed', this.curYearMonth)
            },
            preMonth() {
                this.$EventCalendar.preMonth()
                this.$emit('month-changed', this.curYearMonth)
            },
            handleChangeCurday(date) {
                this.$emit('cur-day-changed', date.date)
            },
            arrowHandler() {
                this.isArrowUp = !this.isArrowUp
                this.$emit('arrow-handler', this.isArrowUp ? 'up' : 'down')
            }
        }
    }
</script>
