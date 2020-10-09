<template>
  <div class="vue-scheduler">
    <h3 class="header">
      <span class="date">{{ getSelectedDate() }}</span>
      <span class="nav" @click="onSelectPrevDay">&lt;</span>
      <span class="nav" @click="onSelectNextDay">&gt;</span>
    </h3>

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
            v-for="(event, index) in eventRow"
            :key="index"
            :style="{ left: event.position + 'px' }"
            :title="getEventTooltip(event)"
          >
            {{ event.name }}
          </div>
        </div>
      </div>
      <div class="now" :style="{ left: now + 'px' }" ref="now"></div>
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
  private eventGrid: any = [[]];
  private now = 0;
  private selectedDate = new Date();

  //Fix algorithm issue with event 5.
  //Add logic for next and previous dates.
  //Publish to npm. Add the tag and everything required. https://zellwk.com/blog/publish-to-npm/
  //Update Readme with installation instructions.
  //Allow customisable event template. Use scoped slots - https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots
  //Maybe later show Date ranges.

  mounted() {
    // console.log('EVENTS: ', this.events);

    this.now =
      moment().hour() * this.eventWidth +
      (moment().minute() * this.eventWidth) / 60;

    //Filter out events that aren't on the selected date.
    const eventsToday = this.events.filter(event => {
      return event.date
        ? this.matchedSelectedDate(event.date, this.selectedDate)
        : false;
    });

    eventsToday.forEach((event: any, index) => {
      const date = moment(event.date);
      const hour = date.hour();
      const minute = date.minute();
      const newEvent = {
        position: hour * this.eventWidth + (minute * this.eventWidth) / 60,
        name: event.name,
        date
      };

      console.log(`EVENT ${index + 1}`, event, newEvent);

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
          } else {
            this.eventGrid[i].push(newEvent);
            break;
          }
        }
      }
    });

    console.log("GRID: ", this.eventGrid);

    //Scroll the "now" marker into view.
    this.$nextTick(() => this.$refs.now.scrollIntoView({ inline: "center" }));
  }

  public matchedSelectedDate(date:any, selectedDate) {
    return (
      date.getDate() === selectedDate.getDate() &&
      date.getMonth() === selectedDate.getMonth() &&
      date.getFullYear() === selectedDate.getFullYear()
    );
  }

  public getSelectedDate() {
    return moment(moment(this.selectedDate)).format("MMM DD, YYYY");
  }

  public getEventTooltip(event: { name: string; date: {} }) {
    return `${event.name} - ${moment(moment(event.date)).format(
      "H:mm A, MMM DD YYYY"
    )}`;
  }

  public onSelectPrevDay() {
    this.selectedDate = moment(moment(this.selectedDate))
      .subtract(1, "days")
      .toDate();
  }

  public onSelectNextDay() {
    this.selectedDate = moment(moment(this.selectedDate))
      .add(1, "days")
      .toDate();
  }
}
</script>

<style scoped>
.header {
  margin: 0 0 10px;
  color: #555;
}

.header .date {
  margin-right: 16px;
  color: #555;
  font-weight: 900;
  font-size: 18px;
}

.header .nav {
  font-size: 16px;
  display: inline-block;
  padding: 0 5px;
  border-radius: 1px;
  font-weight: 700;
  cursor: pointer;
  background: #efefef;
  color: #555;
  margin-right: 5px;
  border-radius: 5px;
}

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

.scheduler-container .now {
  border-left: 1px solid red;
  opacity: 0.5;
  height: 100%;
  position: absolute;
  top: 0px;
  z-index: 0;
}

.scheduler-container .events {
  position: absolute;
  top: 40px;
  z-index: 1;
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
