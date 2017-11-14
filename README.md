# vue-event-calendar-ext

> vue-event-calendar-ext是基于vue-event-calendar定制改造的一个版本。
> [在线例子](https://tkggusraqk.github.io/vue-event-calendar-ext-demo/index.html)

## 依赖
- vue: ^2.0.0

## 使用方法
#### 安装

``` sh
 npm install vue-event-calendar-ext --save
```

#### 入口 Main.js

```javascript
import 'vue-event-calendar-ext/dist/index.css'
import vueEventCalendarExt from 'vue-event-calendar-ext'
Vue.use(vueEventCalendarExt, {locale: 'zh'}) //可以设置语言，支持中文和英文
```

#### 用法示例

```vue
<template>
  <vue-event-calendar :events="demoEvents" @monthChanged="handleMonthChanged" @dayChanged="handleDayChanged"></vue-event-calendar>
</template>

<script>
export default {
  data () {
    return {
      demoEvents: [{
        date: '2016/11/12', // 必填
        title: 'Foo' // 必填
      }, {
        date: '2016/12/15',
        title: 'Bar',
        desc: 'description',
        customClass: 'disabled highlight' // 自定义日历单元格的Class，会加到对应的日历单元格上
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
```

## 自定义日期标题

```vue
<template>
  <vue-event-calendar :title="title" :events="demoEvents" @dayChanged="handleDayChange"></vue-event-calendar>
</template>
```

当你使用自定义日历标题的时候，需要注意，标题将被替换成静态的你传入的String，你需要手动监听dayChanged事件去改变title。

## 组件事件
可以监听的事件有两个，选择了哪天和当月是哪月，当发生改变时，会触发监听函数。函数中的回调参数为改变后的日期。
```
<template>
  <vue-event-calendar
    :events="demoEvents"
    @day-changed="handleDayChanged"
    @month-changed="handleMonthChanged">
  </vue-event-calendar>
</template>
```

## Options

```
  // 用 Vue.use时, 可以设置的参数
  {
    locale: 'zh',
    color: 'black', //Set main color
    className: 'Custom className for current clicked date', // (default: 'selected-day')
    weekStartOn: 'week Start on which day'  // Can be: 1, 2, 3, 4, 5, 6, 0 (default: 0)
  }
```

## API
```javascript
// 下个月
this.$EventCalendar.nextMonth()
```
```javascript
// 上个月
this.$EventCalendar.preMonth()
```
```javascript
//到指定日期
this.$EventCalendar.toDate('2016/11/12')
```

## 开发
可以在github直接clone我的项目然后执行如下命令继续二次开发或发版，欢迎star&&issue
```
npm run dev  //develop
npm run build //production
```

## Change log

##### 1.0.0
- 修复选中日期切换月份仍显示选中状态
- 增加非日程日期可以选中
- 去除日程面板

#Links
[vue-event-calendar](https://github.com/GeoffZhu/vue-event-calendar)