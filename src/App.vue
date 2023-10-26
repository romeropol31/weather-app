<style>
 *{
  margin:0;
  padding: 0;
  box-sizing: border-box;
  }
  body{
    font-family: 'montserrat',sans-serif;
  }

  #app{
    background-image: url('./assets/cold-bg.jpg');
    background-size: cover;
    background-position: bottom;
    transition: 0.4s;
  }

  #app.warm{
    background-image: url('./assets/warm-bg.jpg');
  }

  main{
    min-height: 100vh;
    padding: 25px;
    background-image: linear-gradient(to bottom,rgba(0,0,0,0.25),rgba(0,0,0,0.75));
  }

  .search-box{
    width: 100%;
    margin-bottom: 30px;
    z-index: 1;
    position: relative;
  }

  .search-box .search-bar{
    display: block;
    width: 100%;
    padding: 15px;

    color: #313131;
    font-size: 20px;
    appearance: none;
    border: none;
    outline: none;
    background: none;

    box-shadow: 0px 0px 8px rgba(0,0,0,0.25); 
    background-color: rgba(255,255,255,0.5);
    border-radius: 0px 16px 0px 16px;
    transition: 0.4s;
  }

  .search-box .search-bar:focus{
    box-shadow: 0px 0px 16px rgba(0,0,0,0.25);
    background-color: rgba(255,255,255,0.75);
    border-radius: 16px 0px 16px 0px;
  }
  .location-box .location{
    color: #FFF;
    font-size: 32px;
    font-weight: 500;
    text-align: center;
    text-shadow: 1px 3px rgba(0,0,0,0.25);
  }
  .location-box .date{
    color: #FFF;
    font-size: 20px;
    font-weight: 300;
    font-style: italic;
    text-align: center;
  }

  .weather-box{
    text-align: center;
  }

  .weather-box .temp{
    display: inline-block;
    padding: 10px 25px;
    color: #FFF;
    font-size: 102px;
    font-weight: 900;
    text-shadow: 3px 6px rgba(0,0,0,0.25);
    background-color: rgba(255,255,255,0.25);
    border-radius: 16px;
    margin: 30px 0px;
    box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  }

  .weather-box .weather{
    color: #FFF;
    font-size: 48px;
    font-weight: 700;
    font-style: italic;
    text-shadow: 3px 6px rgba(0,0,0,0.25);
  }

  .snow, .snow:before, .snow:after {
  position: absolute;
  top: -650px;
  left: 0;
  bottom: 0;
  right: 0;
  background-image: 
  radial-gradient(4px 4px at 100px 50px, #fff , transparent), 
  radial-gradient(6px 6px at 200px 150px, #fff, transparent), 
  radial-gradient(3px 3px at 300px 250px, #fff 50%, transparent), 
  radial-gradient(4px 4px at 400px 350px, #fff 50%, transparent), 
  radial-gradient(6px 6px at 500px 100px, #fff 50%, transparent), 
  radial-gradient(3px 3px at 50px 200px, #fff 50%, transparent), 
  radial-gradient(4px 4px at 150px 300px, #fff 50%, transparent), 
  radial-gradient(6px 6px at 250px 400px, #fff 50%, transparent), 
  radial-gradient(3px 3px at 350px 500px, #fff 50%, transparent);
  background-size: 650px 650px;
  animation: snow 3s linear infinite;
  content: "";
}

.snow:after {
  margin-left: -250px;
  opacity: 0.5;
  filter: blur(2px);
  animation-duration: 6s;
  animation-direction: reverse;
}

.snow:before {
	margin-left: -350px;
  opacity: 0.7;
  filter: blur(1px);
  animation-duration: 9s;
  animation-direction: reverse;
}

@keyframes snow {
  to {
    transform: translateY(650px);
  }
}

.rain{
  position: absolute;
  top: -650px;
  left: 0;
  bottom: 0;
  right: 0;
  opacity: 0.5;
  background-image: url('./assets/rain.png');
  animation: rain 0.3s linear infinite;
}

@keyframes rain{
  0%{
    background-position: 0% 0%;
  }
  100%{
    background-position: 20% 100%;
  }
}

.thunderstorm{
  position: absolute;
  top: -650px;
  left: 0;
  bottom: 0;
  right: 0;
  opacity: 0.5;
  background-image: url('./assets/rain.png');
  animation: rain 0.3s linear infinite;
}

.thunderstorm::before{
  content: "";
  position: absolute;
  width: 100%;
  height: 100%;
  background-image: url('./assets/lightining.png');
  top:-100px;
  transform: rotate(180deg);
  animation: lightning 4s linear infinite;
  opacity: 1;
}

@keyframes lightning{
  0%{
    opacity: 0;
  }
  20%{
    opacity: 0;
  }
  21%{
    opacity: 1;
  }
  25%{
    opacity: 0;
  }
  30%{
    opacity: 0;
  }
  31%{
    opacity: 1;
  }
  35%{
    opacity: 0;
  }
  100%{
    opacity: 0;
  }
}


</style>
<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 25 && checkClass == '' ? 'warm' : ''">
    <main>
      <div class="search-box">
        <input type="text" v-model="search" class="search-bar" placeholder="Search..." @keypress="fetchWeather" />
      </div>

      <div class="weather-wrap" v-if="(typeof weather.main != 'undefined')">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ getActualDate() }}</div>
        </div>
        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}ºc</div>
          <div class="weather">{{ weather.weather[0].description }}</div>
        </div>
      </div>

      <div :class="checkClass">
      </div>
    </main>
  </div>
</template>

<script>

export default {
  name: 'App',
  data: () => ({
    api_key: '2d6d7fe3f1a1e8edeff3e5dce6e8e243',
    url_base: 'https://api.openweathermap.org/data/2.5/',
    search: '',
    weather: {},
  }), 
  computed:{
    checkClass(){
      if(!this.weather)return '';
      if(typeof this.weather.main != 'undefined'){
        if(this.weather.weather[0].description.includes('thunderstorm'))return 'thunderstorm';
        if(this.weather.weather[0].description.includes('snow'))return 'snow';
        if(this.weather.weather[0].description.includes('rain'))return 'rain';
      }
      return '';
    },
  },
  methods:{
    fetchWeather (e){
      if(e.key == "Enter"){
        fetch(`${this.url_base}weather?q=${this.search}&units=metric&APPID=${this.api_key}`)
        .then(res => {
          return res.json();
        }).then(this.setResults);
      }
    },
    setResults(res){
      this.weather = res;
    },

    getActualDate(){
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
  },
}
</script>

