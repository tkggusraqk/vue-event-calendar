<template>
    <div class="__vev_calendar-wrapper">
        <cal-panel :events="events" :calendar="calendarOptions" :showCollapse="showCollapse" :arrowBoth="arrowBoth" :selectedDay='selectedDayEvents.date'
                   @cur-day-changed="handleChangeCurDay" @month-changed="handleMonthChanged">
        </cal-panel>
    </div>
</template>
<script>
    import {isEqualDateStr} from './tools.js'

    import calEvents from './components/cal-events.vue'
    import calPanel from './components/cal-panel.vue'

    const inBrowser = typeof window !== 'undefined'
    export default {
        name: 'vue-event-calendar',
        components: {
            'cal-events': calEvents,
            'cal-panel': calPanel
        },
        data() {
            return {
                selectedDayEvents: {
                    date: 'all',
                    events: this.events || []  //default show all event
                }
            }
        },
        props: {
            title: String,
            events: {
                type: Array,
                required: true,
                default: [],
                validator(events) {
                    let validate = true
                    events.forEach((event, index) => {
                        if (!event.date) {
                            console.error('Vue-Event-Calendar-Error:' + 'Prop events Wrong at index ' + index)
                            validate = false
                        }
                    })
                    return validate
                }
            },
            isEventSelected: {
                type: Boolean,
                default: false
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
            calendarOptions() {
                let dateObj = new Date()
                if (inBrowser) {
                    return window.VueCalendarBarEventBus.CALENDAR_EVENTS_DATA
                } else {
                    return {
                        options: {
                            locale: 'zh', //zh
                            color: ' #26a2ff'
                        },
                        params: {
                            curYear: dateObj.getFullYear(),
                            curMonth: dateObj.getMonth(),
                            curDate: dateObj.getDate(),
                            curEventsDate: 'all'
                        }
                    }
                }
            },
            calendarParams() {
                let dateObj = new Date()
                if (inBrowser) {
                    return window.VueCalendarBarEventBus.CALENDAR_EVENTS_DATA.params
                } else {
                    return {
                        curYear: dateObj.getFullYear(),
                        curMonth: dateObj.getMonth(),
                        curDate: dateObj.getDate(),
                        curEventsDate: 'all'
                    }
                }
            }
        },
        created() {
            if (this.calendarParams.curEventsDate !== 'all') {
                this.handleChangeCurDay(this.calendarParams.curEventsDate)
            }
        },
        methods: {
            handleChangeCurDay(date) {
                let events = this.events.filter(function (event) {
                    return isEqualDateStr(event.date, date)
                })
                if ((events.length > 0 && this.isEventSelected) || !this.isEventSelected) {
                    this.selectedDayEvents = {
                        date: date,
                        events: events
                    }
                }
                if (events.length > 0) {
                    this.$emit('day-changed', {
                        date: date,
                        events: events
                    })
                }
            },
            handleMonthChanged(yearMonth) {
                this.$emit('month-changed', yearMonth)
            }
        },
        watch: {
            calendarParams() {
                if (this.calendarParams.curEventsDate !== 'all') {
                    let events = this.events.filter(event => {
                        return isEqualDateStr(event.date, this.calendarParams.curEventsDate)
                    })
                    this.selectedDayEvents = {
                        date: this.calendarParams.curEventsDate,
                        events
                    }
                } else {
                    this.selectedDayEvents = {
                        date: 'all',
                        events: this.events
                    }
                }
            },
            events() {
                this.selectedDayEvents = {
                    date: 'all',
                    events: this.events || []
                }
            }
        }
    }
</script>
<style lang="less">
    @base-orange: #26a2ff;
    @white: #ffffff;
    @gray: #e0e0e0;
    @gray-dark: #b1b1b1;
    @large-padding: 15px;
    @small-padding: 10px;
    @arrow-left-right-border: #333333;
    @arrow-down-up-border: #999999;

    @icon-border-size: 2px;
    @media screen and (min-width: 768px) {
        .__vev_calendar-wrapper {
            max-width: 1200px;
            margin: 0 auto;
            .cal-wrapper {
                width: 50%;
                padding: 100px 50px;
                .date-num {
                    line-height: 50px;
                }
            }
            .events-wrapper {
                width: 50%;
                background-color: @base-orange;
                color: @white;
                padding: 40px 45px;
                position: absolute;
                left: 50%;
                top: 0;
                bottom: 0;
            }
        }
    }

    @media screen and (max-width: 768px) {
        .__vev_calendar-wrapper {
            .cal-wrapper {
                width: 100%;
                padding: 10px 5px;
                .date-num {
                    line-height: 42px;
                }
            }
            .events-wrapper {
                width: 100%;
                margin-top: 10px;
                padding: 10px;
            }
        }
    }

    .__vev_calendar-wrapper {
        position: relative;
        overflow: hidden;
        width: 100%;
        * {
            box-sizing: border-box;
        }
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            box-shadow: inset 0 0 2px rgba(0, 0, 0, 0.2);
            border-radius: 5px;
        }
        ::-webkit-scrollbar-thumb {
            border-radius: 5px;
            background: rgba(0, 0, 0, 0.2);
        }
        .cal-wrapper {
            .cal-header {
                position: relative;
                width: 100%;
                background-color: @white;
                // box-shadow: 0 6px 5px rgba(0,0,0,.1);
                font-weight: 500;
                overflow: hidden;
                padding-bottom: 10px;
                & > div {
                    float: left;
                    line-height: 20px;
                    padding: @large-padding;
                }
                .title {
                    width: 60%;
                    text-align: center;
                    position: relative;
                }
                .l {
                    text-align: left;
                    width: 20%;
                    cursor: pointer;
                    user-select: none;
                    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
                }
                .r {
                    text-align: right;
                    width: 20%;
                    cursor: pointer;
                    user-select: none;
                    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
                }
            }
            .cal-body {
                width: 100%;
                .weeks {
                    width: 100%;
                    overflow: hidden;
                    text-align: center;
                    font-size: 1rem;
                    .item {
                        line-height: 50px;
                        float: left;
                        width: 14.285%;
                        color: #999999;
                    }
                }
                .dates {
                    width: 100%;
                    overflow: hidden;
                    text-align: center;
                    font-size: 1rem;
                    .item {
                        position: relative;
                        float: left;
                        display: block;
                        width: 14.285%;
                        cursor: default;
                        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
                        .date-num {
                            font-size: 1rem;
                            position: relative;
                            z-index: 3;
                        }
                        &.event {
                            cursor: pointer;
                        }
                        &.selected-day {
                            .is-event {
                                background-color: @base-orange;
                            }
                        }
                        .is-event {
                            content: "";
                            background-color: #fff;
                            border-radius: 50%;
                            width: 36px;
                            height: 36px;
                            position: absolute;
                            left: 50%;
                            top: 50%;
                            z-index: 1;
                            margin-left: -18px;
                            margin-top: -19px;
                        }
                        .is-today {
                            content: "";
                            background-color: @base-orange;
                            border-radius: 50%;
                            /*opacity: .8;*/
                            width: 0.3em;
                            height: 0.3em;
                            position: absolute;
                            left: 50%;
                            top: 50%;
                            z-index: 2;
                            margin-left: -2px;
                            margin-top: 8px;
                        }
                    }
                }
            }
            .cal-footer {
                position: relative;
                margin-top: 20px;
                margin-bottom: 30px;
                &:after {
                    width: 200%;
                    height: 200%;
                    position: absolute;
                    top: 0;
                    left: 0;
                    z-index: 0;
                    content: "";
                    -webkit-transform: scale(0.5);
                    -webkit-transform-origin: 0 0;
                    transform: scale(0.5);
                    transform-origin: 0 0;
                    border-bottom: 1px solid #cccccc;
                }
                .container {
                    position: absolute;
                    z-index: 4;
                    left: 50%;
                    width: 44px;
                    height: 44px;
                    margin-top: -15px;
                    background-color: white;
                    margin-left: -22px;
                }
                .arrow {
                    width: 30px;
                    height: 30px;
                    border-radius: 15px;
                    border: 1.5px solid @arrow-down-up-border;
                    position: relative;
                    left: 50%;
                    margin-left: -15px;
                    background-color: white;
                }
                .arrow.down .icon:before {
                    content: "";
                    position: absolute;
                    left: 9px;
                    top: 7px;
                    width: 6px;
                    height: 6px;
                    border-top: solid 2px @arrow-down-up-border;
                    border-right: solid 2px @arrow-down-up-border;
                    -webkit-transform: rotate(135deg);
                    transform: rotate(135deg);
                }
                .arrow.up .icon:before {
                    content: "";
                    position: absolute;
                    left: 9px;
                    top: 11px;
                    width: 6px;
                    height: 6px;
                    border-top: solid 2px @arrow-down-up-border;
                    border-right: solid 2px @arrow-down-up-border;
                    -webkit-transform: rotate(-45deg);
                    transform: rotate(-45deg);
                }
            }
        }
        .events-wrapper {
            border-radius: 10px;
            .cal-events {
                height: 95%;
                overflow-y: auto;
                padding: 0 5px;
                margin: 15px 0;
            }
            .date {
                max-width: 60%;
                min-width: 200px;
                text-align: center;
                color: @white;
                background-color: rgba(0, 0, 0, 0.2);
                border-radius: 20px;
                margin: 0 auto;
                font-size: 22px;
            }
            .event-item {
                padding: 5px 20px;
                margin-top: 15px;
                box-shadow: 0 3px 11px 2px rgba(0, 0, 0, 0.1);
                background-color: #fff;
                border-radius: 5px;
                color: #323232;
                position: relative;
                &:first-child {
                    margin-top: 0;
                }
                .title {
                    height: 40px;
                    line-height: 40px;
                    color: #333333;
                    font-size: 16px;
                    border-bottom: 1px solid #f2f2f2;
                }
                .time {
                    position: absolute;
                    right: 30px;
                    top: 17px;
                    color: #9b9b9b;
                    font-size: 14px;
                }
                .desc {
                    color: #9b9b9b;
                    font-size: 14px;
                    padding: 7px 0;
                }
            }
        }
        .arrow-left.icon {
            color: #000;
            position: absolute;
            left: 6%;
            margin-top: 10px;
        }
        .arrow-left.icon:before {
            content: "";
            position: absolute;
            left: 1px;
            top: -5px;
            width: 10px;
            height: 10px;
            border-top: solid @icon-border-size @arrow-left-right-border;
            border-right: solid @icon-border-size @arrow-left-right-border;
            -webkit-transform: rotate(-135deg);
            transform: rotate(-135deg);
        }
        .arrow-right.icon {
            color: #000;
            position: absolute;
            right: 6%;
            margin-top: 10px;
        }
        .arrow-right.icon:before {
            content: "";
            position: absolute;
            right: 1px;
            top: -5px;
            width: 10px;
            height: 10px;
            border-top: solid @icon-border-size @arrow-left-right-border;
            border-right: solid @icon-border-size @arrow-left-right-border;
            -webkit-transform: rotate(45deg);
            transform: rotate(45deg);
        }
        h3,
        p {
            margin: 0;
            padding: 0;
        }
    }
</style>
