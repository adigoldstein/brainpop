<template>
  <div class="wrapper">
    <div class="info" v-if="this.activity">
      <div class="icon-wrapper" v-bind:class="{jr : this.activity.product === 'bpjr'}">
        <img class="icon"
             :src="require(`../../assets/${this.activity.topic_data.name === 'ada lovelace'?
            'adalovelace' : this.activity.topic_data.name}.png`)"
             alt="icon">
        <div class="jr-bagde" v-if="this.activity.product === 'bpjr'">Jr.</div>
      </div>
      <div class="name-and-date">
          <span class="name">
            {{
              `${this.activity.topic_data.name}
${this.activity.resource_type.replace(/_/g, ' ')}`
            }}
          </span>
        <span class="date">
          {{ new Date(1000 * this.activity.d_created) | moment("dddd, MMMM Do YYYY • h:mm:ss a") }}
        </span>
      </div>
    </div>
    <div class="more">
      <span class="score" v-if="isShowScore(this.activity.resource_type)">
        <span class="no-bold">Score:</span>
        {{ this.activity.score }}/{{ this.activity.possible_score }}
      </span>
      <span class="view-work" v-on:click="onViwWorkClickHandler">
        <b-icon-eye></b-icon-eye>View work
      </span>
    </div>
    <b-icon class="delete-icon" @click="deleteActivityClickHandler()" icon="trash"></b-icon>

  </div>
</template>

<script>
export default {
  name: 'activity',
  props: ['activity'],
  created() {
  },
  methods: {
    isShowScore(resourceType) {
      return resourceType === 'quiz' || resourceType === 'easy_quiz' || resourceType === 'challenge';
    },
    onViwWorkClickHandler() {
      this.$router.push({ name: 'zoom', params: { data: this.activity } });
    },
    deleteActivityClickHandler() {
      this.$emit('deleteActivity', this.activity.id);
    },
  },
};
</script>

<style scoped>

.wrapper {
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: relative;
}

.info {
  display: flex;
}

.name-and-date {
  display: flex;
  flex-direction: column;
}

.name {
  text-transform: capitalize;
  font-weight: bold;
}

.icon-wrapper {
  width: 65px;
  height: 65px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #00CCCB;
  border-radius: 50%;
  margin-right: 7px;
  position: relative;
}

.icon-wrapper.jr {
  background-color: #FDCE67;
}

.icon {
  width: 50px;
}

.jr-bagde {
  position: absolute;
  right: 0;
  bottom: 0;
  font-weight: bold;
  font-size: 12px;
  background-color: #FDCE67;
  border-radius: 50%;
  border: 2px solid #eee;
}

.more {
  color: #02a9a8;
  font-weight: bold;
}

.more .no-bold {
  font-weight: normal;
}

.view-work {
  margin-left: 10px;
}

.delete-icon {
  position: absolute;
  bottom: -6px;
  right: 1px;
  cursor: pointer;
  display: none!important;
}
.activity:hover .delete-icon {
  display: block!important;
}

</style>
