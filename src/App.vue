<template>
  <div id="app" :class="typeof weather.lives != 'undefined' && weather.data.lives[0].temperature > 16 ? 'warm' : ''">
    <main>
      <div class="search-box">
        <input 
          type="text" 
          class="search-bar" 
          placeholder="输入查询....."
          v-model="query"
          @keypress="fetchLocation"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.data != 'undefined'">
        <div class="location-box">
          <div class="location">{{weather.data.lives[0].province}}, {{weather.data.lives[0].city}}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.data.lives[0].temperature) }}°c</div>
          <div class="weather">{{ weather.data.lives[0].weather}}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'app',
  data () {
    return {
      //api_key是高德API的key值35
      api_key: '23c1374382d36d9c416335139a116a',
      url_base: 'https://restapi.amap.com/v3/',
      query: '',
      city: {},
      weather: {}
    }
  },
  methods: {
    fetchLocation(e) {
      if (e.key == "Enter"){
        //作用域问题 解决
        axios.get('https://restapi.amap.com/v3/geocode/geo',{
          params:{address:this.query,key:this.api_key}
        }).then((c) => {
          this.city =c;
          axios.get('https://restapi.amap.com/v3/weather/weatherInfo',{
            params: {city:this.city.data.geocodes[0].adcode,key:this.api_key}
          }).then(this.setResults)
        } )
      }
    },
    //原生js fetch实现

    //fetchLocation (e) {
    //  if (e.key == "Enter") {
    //    fetch(`${this.url_base}geocode/geo?address=${this.query}&key=${this.api_key}`)
    //    .then(res => {
    //     return res.json();
    //    }).then(this.setQuery);
    //  }
    //},
    //setQuery (c) {
    //  this.city = c;
    //  fetch(`${this.url_base}weather/weatherInfo?city=${this.city.geocodes[0].adcode}&key=${this.api_key}`)
    //  .then(res => {
    //    return res.json();
    //  }).then(this.setResults);
    //},
    setResults (results) {
      this.weather = results;
    },
    dateBuilder () {
      let d = new Date();
      let months = ["1月", "2月", "3月", "4月", "5月", "6月", "7月", "8月", "9月", "10月", "11月", "12月"];
      let days = ["周日", "周一", "周二", "周三", "周四", "周五", "周六"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${year}年 ${month} ${date}日 ${day} `;
    }
  }
}
</script>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
}

#app {
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url('./assets/warm-bg.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  
  color: #313131;
  font-size: 20px;

  appearance: none;
  border:none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color:rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
