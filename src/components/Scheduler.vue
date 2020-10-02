<template>
  <div>
    <h1>Scheduler</h1>

    <div class="scheduler-container">
      <div class="timeline">
        <div class="hour">12 am</div>
        <div class="hour">1 am</div>
        <div class="hour">2 am</div>
        <div class="hour">3 am</div>
        <div class="hour">4 am</div>
        <div class="hour">5 am</div>
        <div class="hour">6 am</div>
        <div class="hour">7 am</div>
        <div class="hour">8 am</div>
        <div class="hour">9 am</div>
        <div class="hour">10 am</div>
        <div class="hour">11 am</div>
        <div class="hour">12 pm</div>
        <div class="hour">1 pm</div>
        <div class="hour">2 pm</div>
        <div class="hour">3 pm</div>
        <div class="hour">4 pm</div>
        <div class="hour">5 pm</div>
        <div class="hour">6 pm</div>
        <div class="hour">7 pm</div>
        <div class="hour">8 pm</div>
        <div class="hour">9 pm</div>
        <div class="hour">10 pm</div>
        <div class="hour">11 pm</div>
      </div>

      <div class="events">
        <div class="event-row">
          <div class="event">
            Event name
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class Scheduler extends Vue {
  // @Prop() private msg!: string;

  mounted() {
    // console.log('mounted');
  }
}
</script>

<style scoped>
.scheduler-container {
  width: 100%;
  border: 1px solid #CCC;
  height: 250px;
  overflow-x: scroll;
  position: relative;
}

.scheduler-container .timeline {
  width: 2400px;
  height: 100%;
}

.scheduler-container .timeline .hour {
  width: 100px;
  height: 100%;
  border-right: 1px solid #EEE;
  box-sizing: border-box;
  float: left;
  color: #CCC;
  font-size: 12px;
  text-indent: 5px;
  padding-top: 5px;
}

.scheduler-container .events {
  position: absolute;
  top: 40px;
}

.scheduler-container .events .event {
  width: 150px;
  height: 36px;
  background-color: white;
  border: 1px solid #CCC;
  border-radius: 5px;
  box-sizing: border-box;
  padding: 8px;
  font-size: 12px;
  box-shadow: 2px 2px 5px #DDD;
}
</style>
