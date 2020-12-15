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
            <div  v-for="(activity,index) in filteredActivities"
                 v-bind:key="activity.id" >
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
    };
  },
  methods: {
    onInputChane(e) {
      if (!e.target.value.length) {
        this.filteredActivities = this.activities;
      } else {
        this.filteredActivities = this.filteredActivities
          .filter(a => a.resource_type.replace(/_/g, ' ').includes(e.target.value)
            || a.topic_data.name.includes(e.target.value));
      }
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
          this.filteredActivities = [...this.activities];
        } else {
          this.activities = res.data;
          this.filteredActivities = [...res.data];
        }
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
.activity {
  border: 2px solid #f8ebc6;
  border-radius: 7px;
  margin-bottom: 30px;
  padding: 15px;
}

</style>
