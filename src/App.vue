<template>
  <div id="app">
    <Scheduler :events="eventList" />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Scheduler from "./components/Scheduler.vue";
import moment from "moment";

@Component({
  components: {
    Scheduler
  }
})
export default class App extends Vue {
  private eventList = [
    {
      name: "Test Event",
      date: new Date()
    },
    {
      name: "Test Event 2",
      date: new Date()
    },
    {
      name: "Test Event 3",
      date: moment(new Date()).add(30, 'minutes')._d
    },
    {
      name: "Test Event 4",
      date: moment(new Date()).add(120, 'minutes')._d
    },
  ]
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
