<template>
  <div id="App" :class="(typeof weather.main != 'undefined' && weather.main.temp > 16) ? 'warm' : ''">
  <!-- if文を一行で書く (条件) ? 正 : 誤 -->
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="City Name (like Fukuoka)"
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}℃</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
          <!-- なぜweather.weatherではなくweather.weather[0]なのかはわからん -->
        </div>
      </div>
      <div class="enter-city" v-else>
        Please enter the correct city name.
      </div>
    </main>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'

export default {
  name: "App",
  data() {
    return {
      api_key: "eb5bb8373037ae9f26bbd9b2f6b74194",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
    };
  },
  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then((res) => res.json())
          .then( this.setResults);
        // テンプレート文字列は``でくくれば結合した文字列になる
        // dataで定義されているプロパティであることを示すためにthis.が必要
        // 最後は.then((results) => { this.weather = results; });じゃダメなのか？
      }
    },
    setResults(results){
      this.weather = results;
    },

    dateBuilder () {
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

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#App {
  background-image: url("./assets/cold-bg.jpg");
  background-size: cover;
  /* cover: 縦横比は保持して、背景領域を完全に覆う最小サイズになるように背景画像を拡大縮小する */
  background-position: bottom;
  transition: 0.4s;
}
#App.warm{
  background-image: url("./assets/warm-bg.jpg");
}

main {
  min-height: 100vh;
  /* viewport height 1vh = 1% */
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
  /* 底辺へ向かうグラデーション */
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
  font-size: 16px;

  /* 初期化 */
  appearance: none;
  /*  要素がその環境における標準的なUIのような外観になるよう指定 */
  border: none;
  outline: none;
  /* アウトラインのスタイル・太さ・色をまとめて指定する際に使用 */
  /* borderプロパティとは異なり、上下左右の概念がない（対象が丸い場合アウトラインも丸くなる） */
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
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  /* 100~900(100刻み)でフォントの太さを指定します。太さが9種類用意されているフォントはあまりないため、数値を上下させても太さが変化しないことがあります。標準の太さは400で、太字が700(bold指定ではこれ) */
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  text-align: center;
  font-style: italic;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  /* これがないと１段分左右に大きく広がった要素になる */
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.enter-city{
  text-align: center;
  color: #fff;
  font-size: 16px;
}
</style>
