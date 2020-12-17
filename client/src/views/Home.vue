<template>
  <div class="home">
    <router-view></router-view>
    <b-container>
      <b-row>
        <b-col>
          <div class="msg error-msg" v-if="isError">Ooops, something went wrong :(</div>
          <div class="msg loading-msg" v-else-if="isLoading">
            <b-icon icon="arrow-clockwise" animation="spin" font-scale="4"></b-icon>
          </div>
          <div v-else>
            <h1 class="main-header">Activities View</h1>
            <h2 class="secondery-header">Timeline</h2>
            <section>

              <b-form-input v-model="filterInput" placeholder="Search Timeline"
                            class="filter-input" type="text" v-on:keyup="onInputChane">
              </b-form-input>

              <h3>Filter by:</h3>
              <div class="filter-btns">
                <b-button variant="outline-success" class="filter-btn" size="sm"
                          v-on:click="filterBtnClickHandler('all')">
                  <b-icon icon="check-circle"
                          v-bind:class="[selectedFilter === 'all' ? '' : 'd-none']">
                  </b-icon>
                  All work
                </b-button>
                <b-button variant="outline-success" class="filter-btn"
                          size="sm" v-on:click="filterBtnClickHandler('movie')">
                  <b-icon icon="check-circle"
                          v-bind:class="[selectedFilter === 'movie' ? '' : 'd-none']">
                  </b-icon>
                  Movie
                </b-button>
                <b-button variant="outline-success" class="filter-btn"
                          size="sm" v-on:click="filterBtnClickHandler('quiz')">
                  <b-icon icon="check-circle"
                          v-bind:class="[selectedFilter === 'quiz' ? '' : 'd-none']">
                  </b-icon>
                  Quiz
                </b-button>
                <b-button variant="outline-success" class="filter-btn"
                          size="sm" v-on:click="filterBtnClickHandler('easy_quiz')">
                  <b-icon icon="check-circle"
                          v-bind:class="[selectedFilter === 'easy_quiz' ? '' : 'd-none']">
                  </b-icon>
                  Easy Quiz
                </b-button>
                <b-button variant="outline-success" class="filter-btn"
                          size="sm" v-on:click="filterBtnClickHandler('make_a_map')">
                  <b-icon icon="check-circle"
                          v-bind:class="[selectedFilter === 'make_a_map' ? '' : 'd-none']">
                  </b-icon>
                  Make A Map
                </b-button>
                <b-button variant="outline-success" class="filter-btn"
                          size="sm" v-on:click="filterBtnClickHandler('word_play')">
                  <b-icon icon="check-circle"
                          v-bind:class="[selectedFilter === 'word_play' ? '' : 'd-none']">
                  </b-icon>
                  Word Play
                </b-button>
                <b-button variant="outline-success" class="filter-btn"
                          size="sm" v-on:click="filterBtnClickHandler('related_reading')">
                  <b-icon icon="check-circle"
                          v-bind:class="[selectedFilter === 'related_reading' ? '' : 'd-none']">
                  </b-icon>
                  Related Reading
                </b-button>
                <b-button variant="outline-success" class="filter-btn"
                          size="sm" v-on:click="filterBtnClickHandler('challenge')">
                  <b-icon icon="check-circle"
                          v-bind:class="[selectedFilter === 'challenge' ? '' : 'd-none']">
                  </b-icon>
                  Challenge
                </b-button>
                <b-button variant="outline-success" class="filter-btn"
                          size="sm" v-on:click="filterBtnClickHandler('draw_about_it')">
                  <b-icon icon="check-circle"
                          v-bind:class="[selectedFilter === 'draw_about_it' ? '' : 'd-none']">
                  </b-icon>
                  Draw About It
                </b-button>


              </div>
              <span class="no-results" v-if="!filteredActivities.length">
                No results <b-icon icon="emoji-frown"></b-icon>
              </span>

              <div class="item-wrapper" v-for="(activity,index) in filteredActivities"
                   v-bind:key="activity.id">
              <span class="month" v-if=" index === 0 ||
              new Date(filteredActivities[index - 1].d_created * 1000).getMonth()
              !== new Date(activity.d_created * 1000).getMonth()
              && index < activitiesToShow">
                <span class="border" v-if="index !== 0"></span>
                {{
                  new Date(activity.d_created * 1000) |
                    moment('MMMM')
                }}</span>

                <div class="activity" v-if="index < activitiesToShow">
                  <Activity v-bind:activity="activity" @deleteActivity="deleteActivity($event)"/>
                </div>
                <span class="load-more"
                      v-if="index === activitiesToShow - 1 && index < filteredActivities.length"
                      v-on:click="onShowMore"
                      pill variant="primary">
              <b-icon icon="chevron-down"></b-icon>
              Load more
            </span>
              </div>

            </section>
          </div>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Activity from './Activity.vue';

export default {
  name: 'home',
  components: {
    Activity,
  },
  data() {
    return {
      isError: false,
      isLoading: true,
      filterInput: '',
      activities: [],
      filteredActivities: [],
      activitiesToShow: 10,
      monthDisplay: -1,
      selectedFilter: 'all',
    };
  },
  methods: {
    filterBtnClickHandler(typeToFilter) {
      this.filterInput = '';
      this.selectedFilter = typeToFilter;
      this.filteredActivities = this.activities;
      if (typeToFilter !== 'all') {
        this.filteredActivities = this.filteredActivities
          .filter(a => a.resource_type === typeToFilter);
      }
    },
    onInputChane() {
      if (this.selectedFilter !== 'all') {
        this.filterBtnClickHandler('all');
      }
      this.filteredActivities = this.activities;
      this.filteredActivities = this.filteredActivities
        .filter(a => a.resource_type.replace(/_/g, ' ').includes(this.filterInput)
          || a.topic_data.name.includes(this.filterInput));
    },
    onShowMore() {
      this.activitiesToShow += 10;
    },
    reformatData(data) {
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
    deleteActivity(e) {
      this.activities = this.activities.filter(a => a.id !== e);
      this.filteredActivities = this.filteredActivities.filter(a => a.id !== e);
    },
  },
  created() {
    this.$http.get('http://localhost:3000/activities/v1')
      .then((res) => {
        this.isLoading = false;
        if (Array.isArray(res.data[0].activities)) {
          // V2 endpoint structure support
          this.activities = this.reformatData(res.data);
        } else {
          this.activities = res.data;
        }
        // eslint-disable-next-line no-nested-ternary
        this.activities.sort((a, b) => (a.d_created > b.d_created ? 1
          : (a.d_created < b.d_created) ? -1 : 0));
        this.filteredActivities = [...this.activities];
      })
      .catch((err) => {
        console.log(err);
        console.log('errr');
        this.isError = true;
      });
  },
};
</script>

<style scoped>
.msg {
  text-align: center;
  margin-top: 50vh;
}

.error-msg {
  color: red;
}

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

.filter-btns {
  margin-bottom: 20px;
}

.filter-btn {
  margin-right: 10px;

}

.no-results {
  display: block;
  font-size: 20px;
  text-align: center;
  margin-top: 50px;
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

.border {
  display: block;
  height: 30px;
  width: 2px;
  position: absolute;
  top: -30px;
  left: 50px;
  background-color: #DBDBDB;
}


.item-wrapper:first-child .month:before {
  content: unset;
  background-color: red;

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
  left: 55px;
  display: inline-block;
  content: '';
  width: 2px;
  height: 30px;
  background-color: #DBDBDB;
}

.load-more {
  display: block;
  cursor: pointer;
  color: #00CCCB;
  text-align: center;
  margin-bottom: 50px;
}

</style>
