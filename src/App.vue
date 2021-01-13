<template>
  <div id="app">
    <div class="container">
      <header class="mt-3">
        <h1>空氣品質指標(AQI)</h1>
        <select class="form-control" v-model="filter">
          <option value="">--- 全部行政區 ---</option>
          <option :value="item" v-for="(item, key) in location" :key="key">
            {{ item }}
          </option>
        </select>
      </header>
      <h4>
        想關注的行政區
        <button type="button" class="btn btn-warning u-btn" @click="clearfav()">
          清除關注行政區
        </button>
      </h4>
      <div class="card-columns">
        <CountyAqi
          v-for="(item, key) in tempdata"
          :aqidata="item"
          :key="key"
          @favcounty="favarray"
        ></CountyAqi>
      </div>

      <hr />
      <h4 v-if="filter == ''">所有行政區</h4>
      <h4 v-else>行政區篩選：</h4>
      <div class="card-columns">
        <CountyAqi
          v-for="(item, key) in filtData"
          :key="key"
          :aqidata="item"
          @favcounty="favarray"
        ></CountyAqi>
      </div>
    </div>
  </div>
</template>

<script>
import $ from "jquery";
import CountyAqi from "./components/CountyAqi";

export default {
  name: "App",
  data() {
    return {
      data: [], // 物件陣列
      location: [], // 縣市
      filter: "", // 過濾縣市
      favorite: [], // 關注的城市
    };
  },
  methods: {
    getData() {
      const vm = this;
      const api =
        "https://cors-anywhere.herokuapp.com/http://opendata2.epa.gov.tw/AQI.json";
      $.get(api).then(function (res) {
        vm.data = res;
        //add starStatus to each object
        vm.data.forEach(function (item) {
          if (vm.favorite.includes(item.SiteName)) {
            vm.$set(item, "favorite", true);
          } else {
            vm.$set(item, "favorite", false);
          }
        });
        //county filter
        vm.data.forEach(function (item) {
          if (!vm.location.includes(item.County)) {
            vm.location.push(item.County);
          }
        });
      });
      // get from localStorage
      if (localStorage.favorite !== undefined) {
        this.favorite = localStorage.getItem("favorite").split(",");
      }
    },
    //create favorite array
    favarray(item) {
      if (!this.favorite.includes(item.SiteName)) {
        this.favorite.push(item.SiteName);
      } else {
        this.favorite.splice(this.favorite.indexOf(item.SiteName), 1);
      }
      // set to localStorage
      localStorage.setItem("favorite", this.favorite);
    },
    //clear favorite & localStorage
    clearfav() {
      const vm = this;
      vm.favorite = [];
      vm.data.forEach(function (item) {
        item.favorite = false;
      });
      localStorage.clear();
    },
  },
  computed: {
    filtData() {
      var vm = this;
      if (vm.filter == "") {
        return vm.data;
      } else {
        return vm.data.filter(function (item) {
          return item.County == vm.filter;
        });
      }
    },
    tempdata() {
      var temp = [];
      const vm = this;
      vm.data.forEach(function (item) {
        if (vm.favorite.includes(item.SiteName)) {
          temp.push(item);
        }
      });
      return temp;
    },
  },

  mounted() {
    this.getData();
  },
  components: {
    CountyAqi,
  },
};
</script>

<style>
* {
  position: relative;
}
body {
  font-family: "Noto Sans TC", sans-serif;
  background-color: #f1f1f1;
}
header {
  text-align: center;
}
header h1 {
  color: #4e709d;
  margin-bottom: 30px;
}
h4 {
  text-align: center;
}
select {
  margin: 24px 0;
}
.u-btn {
  position: absolute;
  top: 65%;
  right: 0;
  transform: translateY(-50%);
}
</style>
