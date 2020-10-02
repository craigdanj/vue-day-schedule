<template>
  <div id="app">
    <Scheduler :events="eventList" />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Scheduler from "./components/Scheduler.vue";

@Component({
  components: {
    Scheduler
  }
})
export default class App extends Vue {
  private eventList = [
    {
      name: "Test Event",
      time: new Date()
    },
    {
      name: "Test Event 2",
      time: new Date()
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
