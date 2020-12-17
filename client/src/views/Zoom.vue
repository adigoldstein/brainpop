<template>
  <div class="bg">
    <div class="box" v-if="isError">
      <div class="comment">Oops...</div>
      <router-link to="../">Lets choose activity first....</router-link>
    </div>
    <div class="box" v-if="!isError">
      <router-link
        to="../"
        tag="span">
        <b-icon class="close h1" icon="x-circle"></b-icon>
      </router-link>
      <div class="top">
        <div class="icon-wrapper" v-bind:class="{jr : this.activity.product === 'bpjr'}">
          <img class="icon"
               :src="require(`../../assets/${this.activity.topic_data.name === 'ada lovelace'?
            'adalovelace' : this.activity.topic_data.name}.png`)"
               alt="icon">
          <div class="jr-bagde" v-if="this.activity.product === 'bpjr'">Jr.</div>
        </div>
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
      <div class="comment">{{ activity.comment }}</div>
      <span class="score" v-if="isShowScore(this.activity.resource_type)">
           <span class="no-bold">Score:</span>
        {{ this.activity.score }}/{{ this.activity.possible_score }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'zoom',
  data() {
    return {
      activity: null,
      isError: false,
    };
  },
  created() {
    this.activity = this.$route.params.data;
    if (typeof this.activity === 'undefined') {
      this.isError = true;
    }
  },
  methods: {
    isShowScore(resourceType) {
      return resourceType === 'quiz' || resourceType === 'easy_quiz' || resourceType === 'challenge';
    },
  },
};
</script>

<style scoped>
.bg {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto; /* Enable scroll if needed */

  display: flex;
  justify-content: center;
  align-items: center;
}

.box {
  background-color: #fff;
  border: 5px solid #BBBBBB;
  border-radius: 30px;
  width: 600px;
  position: absolute;
  padding: 25px;
}

.close {
  position: absolute;
  top: 14px;
  right: 14px;
  font-size: 2.5rem;
}

.top {
  display: flex;
  flex-direction: column;
  text-align: center;
}

.icon-wrapper {
  margin: 0 auto 20px;
  width: 65px;
  height: 65px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #00CCCB;
  border-radius: 50%;
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

.name {
  font-size: 28px;
  font-weight: bold;
  text-transform: capitalize;
  margin-bottom: 20px;
}

.date {
  color: #BBBBBB;
  font-weight: bold;
  margin-bottom: 20px;
}

.comment {
  font-size: 24px;
  margin-bottom: 20px;
}

.score {
  color: #02a9a8;
  font-weight: bold;
}

.no-bold {
  font-weight: normal;
}
</style>
