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
            v-for="event in eventRow"
            :key="event.date.toString()"
            :style="{ left: event.position + 'px' }"
            :title="getEventTooltip(event)"
          >
            {{ event.name }}
          </div>
        </div>
      </div>
      <div
        class="now"
        :style="{ left: nowMarkerPosition + 'px' }"
        ref="now"
      ></div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from "vue-property-decorator";
import moment from "moment";

@Component
export default class Scheduler extends Vue {
  @Prop() private events!: [];

  private eventWidth = 100;
  private eventGrid: any = [[]];
  private nowMarkerPosition = 0;
  private selectedDate = new Date();

  //Publish first version to npm. Add the tag and everything required. https://zellwk.com/blog/publish-to-npm/
  //Update Readme with installation instructions.
  //Allow customisable event template. Use scoped slots - https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots
  //Maybe later show Date ranges.
  //Truncate long event names to prevent themspilling out of the event containers.
  //Make length of event without end date configurable.
  //(Done? Test) Fix algorithm issue with event 5. (Clash condition needs to change. Needs to check if whole range clashes not just start time.) - https://stackoverflow.com/questions/325933/determine-whether-two-date-ranges-overlap (check momentjs answer)

  @Watch("events")
  onEventsChanged() {
    this.arrangeEvents();
  }

  mounted() {
    this.nowMarkerPosition =
      moment().hour() * this.eventWidth +
      (moment().minute() * this.eventWidth) / 60;

    this.arrangeEvents();

    //Scroll the "now" marker into view.
    //@ts-ignore
    this.$nextTick(() => this.$refs.now.scrollIntoView({ inline: "center" }));
  }

  public arrangeEvents() {
    this.eventGrid = [[]];

    //Filter out events that aren't on the selected date.
    const eventsToday = this.events.filter(({ date }) => {
      return date ? this.matchedSelectedDate(date, this.selectedDate) : false;
    });

    eventsToday.forEach((event: { date: Date; name: string }, index) => {
      const date = moment(event.date);
      const dateEdge = moment(date).add(1, "hours");
      const hour = date.hour();
      const minute = date.minute();
      const newEvent: { position: number; name: string; date: {} } = {
        position: hour * this.eventWidth + (minute * this.eventWidth) / 60,
        name: event.name,
        date
      };

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

            if (this.doDatesOverlap(date, dateEdge, eventStart, eventEdge)) {
              clash = true;
              console.log("CLASH OF THE TIME!!", event.name);
            } else {
              console.log("NO CLASH OF THE TIME!!", event.name);
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
  }

  public doDatesOverlap(
    startDate1: any,
    endDate1: any,
    startDate2: any,
    endDate2: any
  ) {
    return (
      moment(startDate1).isSameOrBefore(endDate2) &&
      moment(startDate2).isSameOrBefore(endDate1)
    );
  }

  public matchedSelectedDate(date: Date, selectedDate: Date) {
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

    this.$emit("vs-date-change", this.selectedDate);
  }

  public onSelectNextDay() {
    this.selectedDate = moment(moment(this.selectedDate))
      .add(1, "days")
      .toDate();
    this.$emit("vs-date-change", this.selectedDate);
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
  /* Keep this width equal to the eventWidth variable */
  width: 100px;
  height: 36px;
  background-color: white;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
  padding: 8px;
  font-size: 12px;
  box-shadow: 2px 2px 5px #ddd;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

</style>
