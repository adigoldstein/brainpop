<template>
  <div class="home">
    <b-container>
      <b-row>
        <b-col>
          <h1 class="main-header">Activities View</h1>
          <h2 class="secondery-header">Timeline</h2>
          <b-form-input placeholder="Filter results"
                        class="filter-input" type="text" v-on:keyup="onInputChane">
          </b-form-input>
          <section v-if="filteredActivities.length">
            <div class="item-wrapper"  v-for="(activity,index) in filteredActivities"
                 v-bind:key="activity.id" >
              <span class="month" v-if=" index === 0 ||
              new Date(filteredActivities[index - 1].d_created * 1000).getMonth()
              !== new Date(activity.d_created * 1000).getMonth()
              && index < activitiesToShow">
                {{ new Date(activity.d_created * 1000) |
                moment('MMMM') }}</span>

              <div class="activity" v-if="index < activitiesToShow">
                <Activity  v-bind:activity="activity" />
              </div>
            </div>
            <b-button v-on:click="onShowMore" pill variant="primary">Load more</b-button>
          </section>
        </b-col>
      </b-row>
    </b-container>

  </div>
</template>

<script>
// @ is an alias to /src
import Activity from './activity.vue';

export default {
  name: 'home',
  components: {
    Activity,
  },
  data() {
    return {
      activities: [],
      filteredActivities: [],
      activitiesToShow: 5,
      monthDisplay: -1,
    };
  },
  methods: {
    onInputChane(e) {
      this.filteredActivities = this.activities;
      this.filteredActivities = this.filteredActivities
        .filter(a => a.resource_type.replace(/_/g, ' ').includes(e.target.value)
            || a.topic_data.name.includes(e.target.value));
    },
    onShowMore() {
      this.activitiesToShow += 5;
    },
    parseData(data) {
      const parsedArray = [];
      // eslint-disable-next-line no-restricted-syntax
      for (const item of data) {
        // eslint-disable-next-line no-restricted-syntax
        for (const activity of item.activities) {
          const newActivity = activity;
          newActivity.resource_type = item.resource_type;
          parsedArray.push(newActivity);
        }
      }
      return parsedArray;
    },
  },
  created() {
    this.$http.get('http://localhost:3000/activities/v2')
      .then((res) => {
        if (Array.isArray(res.data[0].activities)) {
          // V2 endpoint data
          this.activities = this.parseData(res.data);
        } else {
          this.activities = res.data;
        }
        console.log(this.activities);
        // eslint-disable-next-line no-nested-ternary
        this.activities.sort((a, b) => (a.d_created > b.d_created ? 1
          : (a.d_created < b.d_created) ? -1 : 0));
        this.filteredActivities = [...this.activities];
      });
    // .catch(err => console.log(err));
  },
};
</script>

<style scoped>

.main-header {
  margin-top: 40px;
  font-weight: bold;
  letter-spacing: -0.5px;
}
.secondery-header {
  margin-top: 25px;
}
.filter-input {
  width: 25%;
  margin-bottom: 15px;
}

.month {
  display: block;
  text-align: center;
  padding: 4px 0;
  background-color: #FCF8E5;
  border-radius: 30px;
  font-weight: bold;
  width: 100px;
  margin-bottom: 30px;
  margin-left: 10px;
  position: relative;
}
.month:before {
  content: '';
  height: 30px;
  width: 2px;
  position: absolute;
  top: -30px;
  left: 50px;
  background-color: #DBDBDB;
}
.item-wrapper:first-child .month:before {
  content: unset;
}

.activity {
  border: 2px solid #DBDBDB;
  border-radius: 7px;
  margin-bottom: 28px;
  padding: 15px;
  position: relative;
}
.activity:before {
  position: absolute;
  top: -30px;
  left: 55px;  display: inline-block;
  content: '';
  width: 2px;
  height: 30px;
  background-color: #DBDBDB;
}

</style>
