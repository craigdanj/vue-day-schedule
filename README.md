# Vue Day Schedule
View your day's schedule along a horizontal timeline. Built for VueJS. (Documentation is still work in progress. Contributions to welcome.)


## Install via npm
```
npm install vue-day-schedule
```

## Setup 
First include the CSS file in your main.js (or main.ts if you use Typescript). You could also add this to the file you wish to use the scheduler in.

```
import 'vue-day-schedule/dist/VueDayScheduler.css';
```

Then import the component and use it way you would normally use a component.

```
import vueDaySchedule from 'vue-day-schedule';

<template>
    <vue-day-schedule 
        :events="eventList"
        v-on:vs-date-change="handleDateChange"
    />
</template>

<script>
import vueDaySchedule from 'vue-day-schedule';

export default {
    components: {
        vueDaySchedule
    },
    data () {
        eventList: []
    },
    methods () {
        handleDateChange: function (date) {
            alert("Date changed");
        }
    }
}
</script>

```



## Development setup to contribute
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```
