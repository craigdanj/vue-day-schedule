# Vue Day Schedule
View your day's schedule along a horizontal timeline. Built for VueJS. (Documentation is still work in progress.)


## Install via npm
```bash
npm install vue-day-schedule
```

## Setup 
First include the CSS file in your main.js (or main.ts if you use Typescript). You could also add this to the file you wish to use the scheduler in.

```js
import 'vue-day-schedule/dist/VueDayScheduler.css';
```

Then import the component and use it way you would normally use a component.

```js
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
        eventList: [
            {
                name: "Test Event", //Name of the event
                date: new Date()    //Start time of the event as a JS Date object
            }
        ]
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
```bash
npm install
```

### Compiles and hot-reloads for development
```bash
npm run serve
```

Please feel free to raise issues or feature requests. If feature requests align with the goal of this project we will probably work on adding it in.
