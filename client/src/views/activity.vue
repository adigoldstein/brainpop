<template>
  <div class="wrapper">
    <div class="info" v-if="this.activity">
      <div class="icon-wrapper" v-bind:class="{jr : this.activity.product === 'bpjr'}">
        <img class="icon"
             :src="require(`../../assets/${this.activity.topic_data.name === 'ada lovelace'?
            'adalovelace' : this.activity.topic_data.name}.png`)"
             alt="icon">
      </div>
      <div class="name-and-date">
          <span class="name">
            {{
              `${this.activity.topic_data.name}
${this.activity.resource_type.replace(/_/g, ' ')}`
            }}
          </span>
        <span class="date"> {{ new Date(1000 * this.activity.d_created) }}</span>
      </div>
    </div>
    <div class="more">
      <span class="score" v-if="isShowScore(this.activity.resource_type)">
        <span class="no-bold">Score:</span> {{this.activity.score}}/{{this.activity.possible_score}}
      </span>
      <span class="view-work">    <b-icon-eye></b-icon-eye>
View work</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'activity',
  props: ['activity'],
  created() {
    console.log(this.$props.activity);
  },
  methods: {
    isShowScore(resourceType) {
      return resourceType === 'quiz' || resourceType === 'easy_quiz' || resourceType === 'challenge';
    },
  },
};
</script>

<style scoped>

.wrapper {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.info {
  display: flex;
}
.name-and-date {
  display: flex;
  flex-direction: column;
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
.more {
  color: #02a9a8;
  font-weight: bold;
}
.more .no-bold{
  font-weight: normal;
}

</style>
