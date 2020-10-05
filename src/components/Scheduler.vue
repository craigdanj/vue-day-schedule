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
        <div class="event-row" v-for="(eventRow, i) in eventGrid" :key="i">
          <div
            class="event"
            v-for="(event, index) in eventGrid[0]"
            :key="index"
            :style="{ left: event.position + 'px' }"
          >
            {{ event.name }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import moment from "moment";

@Component
export default class Scheduler extends Vue {
  @Prop() private events!: [];

  private eventWidth = 100;
  private eventGrid = [[]];

  mounted() {
    this.events.forEach((event, index) => {
      const date = moment(event.date);
      const hour = date.hour();
      const minute = date.minute();
      const newEvent = {
        position: hour * this.eventWidth + (minute * this.eventWidth) / 60,
        name: event.name,
        date
      };

      //Start with first row.
      //Iterate through add items to detect clashes.
      //If no clash push to row.
      //Else check if next row exists and look for clashes in it.
      //Else add a row and push event onto it.

      if (index === 0) {
        this.eventGrid[0].push(newEvent);
      } else {
        //Loop through eventGrid rows.
        for (let i = 0; i < this.eventGrid.length; i++) {
          //Loop through i'th row items to detect clash.
          let clash = false;

          for (let j = 0; j < this.eventGrid[i].length; j++) {
            const eventDate = moment(this.eventGrid[i][j].date);

            const eventStart = moment(eventDate);
            const eventEdge = eventDate.add(1, "hours");

            console.log(">>", moment(date)._d);
            console.log(eventStart._d);
            console.log(eventEdge._d);

            if (
              moment(date).isBetween(eventStart, eventEdge) ||
              moment(date).isSame(eventStart) ||
              moment(date).isSame(eventEdge)
            ) {
              clash = true;
              console.log("CLASH OF THE TIME!!");
            } else {
              console.log("NO CLASH OF THE TIME!!");
            }
          }

          if (clash) {
            if (this.eventGrid[i + 1]) {
              continue;
            } else {
              this.eventGrid.push([]);
              this.eventGrid[i + 1].push(newEvent);
              break;
            }
          }
        }
      }

      //V1 CODE
      // for (let i = 0; i < this.eventGrid.length; i++) {
      //   if (this.eventGrid[i].length) {
      //     let clashDetected = false;

      //     //Iterate through event row items to find clashes.
      //     for (let j = 0; j < this.eventGrid[i].length; j++) {
      //       console.log(this.eventGrid[i][j], event);

      //       const eventDate = moment(moment(this.eventGrid[i][j].date));

      //       if (
      //         moment(date).isBetween(
      //           moment(this.eventGrid[i][j].date),
      //           eventDate.add(1, "hours")
      //         )
      //       ) {
      //         //Add clash condition here
      //         clashDetected = true;
      //         console.log('CLASH OF THE TIME!!')
      //       }
      //     }

      //     if (!clashDetected) {
      //       this.eventGrid[i].push({
      //         position:
      //           hour * this.eventWidth + (minute * this.eventWidth) / 60,
      //         name: event.name,
      //         date
      //       });
      //     }
      //   } else {
      //     this.eventGrid[i].push({
      //       position: hour * this.eventWidth + (minute * this.eventWidth) / 60,
      //       name: event.name,
      //       date
      //     });
      //   }
      // }

      //OLD CODE
      // this.eventGrid[0].push({
      //   position: hour * this.eventWidth + (minute * this.eventWidth) / 60,
      //   name: event.name
      // });
    });

    console.log("GRID: ", this.eventGrid);
  }
}
</script>

<style scoped>
.scheduler-container {
  width: 100%;
  border: 1px solid #ccc;
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
  border-right: 1px solid #eee;
  box-sizing: border-box;
  float: left;
  color: #aaa;
  font-size: 12px;
  text-indent: 5px;
  padding-top: 5px;
}

.scheduler-container .events {
  position: absolute;
  top: 40px;
}

.scheduler-container .events .event-row {
  margin-bottom: 10px;
  height: 36px;
  position: relative;
}

.scheduler-container .events .event {
  position: absolute;
  top: 0px;
  width: 150px;
  height: 36px;
  background-color: white;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
  padding: 8px;
  font-size: 12px;
  box-shadow: 2px 2px 5px #ddd;

  
}
</style>
