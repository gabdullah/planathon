<template>
  <div id="dashboard" v-if="$parent.user">
    <div id="catagory-tabs">
      <div class="light-gray">Update me on: </div>
        <div class="catagory-tab yellow">Finances</div>
        <div class="catagory-tab orange">Development</div>
        <div class="catagory-tab pink">Promotion</div>
        <div class="catagory-tab purple">Design</div>
        <div class="catagory-tab blue">General Logistics</div>
        <div class="catagory-tab">All</div>
    </div>

  <!--Calendar on the side -- TODO: Abstract to a component?-->
    <div id="calendar">
      <div id="day-labels">
        <span>M</span>
        <span>T</span>
        <span>W</span>
        <span>R</span>
        <span>F</span>
        <span>S</span>
        <span>S</span>
      </div>
      <div id="day-nodes">
        <div class="week-node" v-for="week in 35">
          <div v-for="i in 7" class="day-node">
          </div>
        </div>
      </div>
    </div>


    <div class="widget-holder">
      <tasks :timeline="timeline">
      </tasks>
      <div class="dark-widget">
      </div>
    </div>

  </div>
  <div v-else>
    <div id="calendar"></div>
  </div>
</template>

<script>
import LineGraph from '@/components/Charts/LineGraph.js';
import PolarGraph from '@/components/Charts/PolarGraph.js';
import Tasks from '@/components/dashboardComponents/tasks.vue';

export default {
  data() {
    return {
      hackathonId: this.$route.params.hackathonId,
      timeline: [],
      hackathonTasks: []
    }
  },
  mounted() {
    // Get the timeline for the hackathon
    this.$parent.db.collection('hackathons').doc(this.hackathonId).get().then((doc) => {
      this.timeline = doc.data().timeline;

      // For each task id in the timeline, get the actual task object
      // and put the tasks in hackathonTasks
      this.timeline.forEach((task) => {
        this.$parent.db.collection('tasks').doc(task).get().then((doc) => {
          this.hackathonTasks.push(doc.data());
        }).catch((err) => {
          console.error("Error getting the hackathon's tasks: ", err);
        })
      })
    }).catch((err) => {
      console.error("Error getting the hackathon's timeline: ", err);
    })
  },
  components: {
    LineGraph,
    PolarGraph,
    Tasks
  }
}
</script>

<style scoped lang="scss">
@import '@/GlobalVars.scss';

#dashboard {
  
  width: calc(100% - 180px);
  margin-right: 0px;
  margin-left: auto;
}
.widget-holder {
  display: flex;
  justify-content: space-evenly;
}

.dark-widget {
  background-color: $dark-gray;
  width: 40%;
  height: 500px;
  margin-top: 25px;
}

.task-widget {
  background-color: $gray;
  width: 80%;
  min-height: 75px;
  margin: 15px 5%;
  padding: 10px 5%;
  text-align: left;
}

.task-circle {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  margin-right: 5px;
}

h3, h4 {
  margin: 0;
}

h4 {
  opacity: .5;
}

#calendar {
  width: 180px;
  height: 100%;
  position: absolute;
  background: $gray;
  left: 0px;
  top: 0px;
  display: grid;
  grid-template-columns: 20% 80%;
  grid-template-rows: 5% 95%;
  font-family: courier;
}
#day-labels {
  grid-column: 2/3;
  grid-row: 1/2;
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding-right: 5px;
}
#day-nodes {
  grid-column: 2/3;
  grid-row: 2/3;
  display: flex;
  flex-direction: column;
  overflow-y: scroll;
  padding-right: 5px;
}
.day-node {
  width: 10px;
  height: 10px;
  background: $lighter-gray;
}
.week-node {
  display: flex;
  justify-content: space-around;
  margin-bottom: 10px;
  display: flex;
}

#catagory-tabs {
  width: 100%;
  height: 50px;
  background: $gray;
  display: flex;
  align-items: center;
  div {
    margin-left: 20px;
  }
}

.light-gray {
  color: $lighter-gray;
}

.catagory-tab {
  font-weight: bolder;
  font-size: 18px;
  cursor: pointer;
}
</style>
