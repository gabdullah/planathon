<template>
  <div id="dashboard" v-if="$parent.user">
    <div class="dark-widget">
      <div class="task-widget" v-for="task in tasks">
        <div style="display: flex">
          <div class="task-circle" v-for="tag in task.tags"
            :class="[tag]"
          ></div>
        </div>
        <h3>{{ task.title }}</h3>
        <h4>{{ task.description }}</h4>
      </div>
    </div>
    <div class="dark-widget">
      <div class="task-widget" id="timeline">
      </div>
    </div>
  </div>
</template>

<script>
import vis from 'vis';
import 'vis/dist/vis.min.css';

import LineGraph from '@/components/Charts/LineGraph.js';
import PolarGraph from '@/components/Charts/PolarGraph.js';

export default {
  data() {
    return {
      tasks: [
        {
          title: "Swag: T-shirts",
          description: "Design and order t-shirts for the event.",
          tags: [
            "finance",
            "design"
          ]
        },
        {
          title: "Second Wave of Sponsor Emails",
          description: "Remind sponsors why you're worth it.",
          tags: [
            "promotion"
          ]
        }
      ],
      groups: [{
        id: 0,
        content: 'Group 1'
      }],
      items: [{
        id: 0,
        group: 0,
        start: new Date(),
        content: 'Item 1'
      }],
      options: {
        editable: true,
      },
      container: '',
      timeline: null,
    }
  },
  methods: {

  },
  components: {
    LineGraph,
    PolarGraph,
  },
  mounted() {
    setTimeout(() => {
      this.container = document.getElementById('timeline');
      this.timeline = new vis.Timeline(this.container, this.items, this.groups, this.options);
    }, 1000);
    
  }
}
</script>

<style scoped lang="scss">
@import '@/GlobalVars.scss';

#dashboard {
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

</style>