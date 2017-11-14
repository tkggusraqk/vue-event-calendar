<template>
    <div id="app">
        <h1>vue-event-calendar-ext</h1>
        <vue-event-calendar
                :events="demoEvents"
                @day-changed="handleDayChanged"
                @month-changed="handleMonthChanged"
        ></vue-event-calendar>
        <h2 class="t-center">禁用折叠面板</h2>
        <vue-event-calendar
                :events="demoEvents"
                :showCollapse="false"
                @day-changed="handleDayChanged"
                @month-changed="handleMonthChanged"
        ></vue-event-calendar>
        <h2 class="t-center">导航两边对齐</h2>
        <vue-event-calendar
                :events="demoEvents"
                :showCollapse="false"
                :arrowBoth="true"
                @day-changed="handleDayChanged"
                @month-changed="handleMonthChanged"
        ></vue-event-calendar>
        <h2 class="t-center">非日程不能选中</h2>
        <vue-event-calendar
                :events="demoEvents"
                :showCollapse="false"
                :arrowBoth="true"
                :isEventSelected="true"
                @day-changed="handleDayChanged"
                @month-changed="handleMonthChanged"
        ></vue-event-calendar>
    </div>
</template>

<script>
    let today = new Date()
    export default {
        name: 'app',
        data() {
            return {
                demoEvents: [{
                    date: `${today.getFullYear()}/${today.getMonth() + 1}/15`,
                    title: 'Title-1',
                    desc: 'longlonglong description'
                }, {
                    date: `${today.getFullYear()}/${today.getMonth() + 1}/24`,
                    title: 'Title-2'
                }, {
                    date: `${today.getFullYear()}/${today.getMonth() === 11 ? 1 : today.getMonth() + 2}/06`,
                    title: 'Title-3',
                    desc: 'description'
                }, {
                    date: `${today.getFullYear()}/${today.getMonth() + 1}/02`,
                    title: 'Title-3',
                    desc: 'description'
                }]
            }
        },
        methods: {
            handleDayChanged(data) {
                //日程事件处理 todo
                console.log(data)
            },
            handleMonthChanged(data) {
                //月份切换事件 todo
                console.log(data)
            }
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        color: #2c3e50;
        margin-top: 30px;
    }

    h1, h2, h3 {
        font-weight: normal;
        margin: 0;
        padding: 0;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #42b983;
    }

    .t-center {
        text-align: center;
        margin: 20px;
    }
</style>
