<template>
  <div class="card" :class="status">
    <div class="card-header">
      {{ aqidata.County }} - {{ aqidata.SiteName }}
      <a href="#" class="float-right" @click.prevent="likes">
        <i class="fa-star" :class="aqidata.favorite ? 'fas' : 'far'"></i>
      </a>
    </div>
    <div class="card-body">
      <ul class="list-unstyled">
        <li>AQI 指數：{{ aqidata.AQI }}</li>
        <li>PM2.5： {{ aqidata["PM2.5"] }}</li>
        <li>說明：{{ aqidata.Status }}</li>
      </ul>
      <span class="time">{{ aqidata.PublishTime }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "CountyAqi",
  props: ["aqidata"],
  computed: {
    /* 參考他人作法 */
    status: function () {
      if (this.aqidata.AQI <= 50) {
        return "status-aqi1";
      } else if (this.aqidata.AQI <= 100) {
        return "status-aqi2";
      } else if (this.aqidata.AQI <= 150) {
        return "status-aqi3";
      } else if (this.aqidata.AQI <= 200) {
        return "status-aqi4";
      } else if (this.aqidata.AQI <= 300) {
        return "status-aqi5";
      } else {
        return "status-aqi6";
      }
    },
  },
  methods: {
    likes: function () {
      this.aqidata.favorite = !this.aqidata.favorite;
      this.$emit("favcounty", this.aqidata);
    },
  },
};
</script>

<style scoped>
.card-header {
  font-weight: bold;
}
.time {
  font-size: 14px;
  font-style: italic;
}
.fa-star {
  color: #0b8457;
}
.status-aqi1 {
  background-color: #ffe578;
}

.status-aqi2 {
  background-color: #fff1bc;
}

.status-aqi3 {
  background-color: #ebebeb;
}

.status-aqi4 {
  background-color: #ffc7c7;
}

.status-aqi5 {
  background-color: #ff9393;
}

.status-aqi6 {
  background-color: #ff6767;
}
</style>>

