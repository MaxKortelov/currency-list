<template>
  <div id="app">
    <div class="mobile">Do not support mobile devices. Try on tablet or PC</div>
    <div class="pre" v-if="preloader">
      <img src="./assets/preloader.gif">
    </div>
    <MainPage :period="period"
              :info="info"
    />
  </div>
</template>

<script>
import MainPage from './components/MainPage.vue'

export default {
  name: 'App',
  components: {
    MainPage
  },
  data() {
    return {
      api: 'https://min-api.cryptocompare.com/data/v2/',
      url: 'https://min-api.cryptocompare.com/data/price?',
      period: ['histoday?', 'histohour?', 'histominute?'],
      end: '',
      limit: '&limit=10',
      date: this.isDate,
      currency: '',
      info: 0,
      timer: '',
      temp: '',
      preloader: true,
    }
  },
  computed: {
    isDate: function() {
      return String(new Date()).slice(15, 24)
    }
  },
  methods: {
    getCurrency: function () {
      clearTimeout(this.timer);
      let url = `${this.url.slice(0, this.url.lastIndexOf('='))}s${this.url.slice(this.url.lastIndexOf('='), this.url.length)}`;
      try {
        fetch(url)
          .then(data => data.json())
          .then(json => {
            this.info = json[this.currency];
            let newCharData = {...this.$children[0].$children[0]._data.charData};
            if(newCharData.datasets[0].data[newCharData.datasets[0].data.length - 1] !== String(this.info)) {
              if(newCharData.datasets[0].data.length < 5) {
                newCharData.datasets[0].data.push(String(this.info))
              } else {
                newCharData.datasets[0].data.shift();
                newCharData.datasets[0].data.push(String(this.info));
              }
            }
            this.$children[0].$children[0]._data.charData = {...newCharData};
             this.timer = setTimeout(() => {
              if(json) this.getCurrency();
            }, 5000)
          })
          .catch(err => console.log(err))
      } catch {
        console.log('err');
      }
  }
  },
  watch: {
    end() {
      if (this.end.length >= 17) {
        this.url = this.url.slice(0, 45) + this.end;
        this.getCurrency();
      }
    }
  },
  mounted() {
    setTimeout(() => {this.preloader = false}, 5000);
  }
}
</script>

<style>
  body {
    margin: 0;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
  }

#app {
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100%;
  height: 100%;
}

.pre {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: white;
  top: 0;
  left: 0;
  position: absolute;
  width: 100vw;
  height: 100vh;
  z-index: 100;
}

.mobile {
  display: none;
}

  @media (max-width: 900px) {
    .mobile {
      display: flex;
      justify-content: center;
      align-items: center;
      background-color: white;
      width: 100vw;
      height: 100vh;
      position: fixed;
      font-weight: bold;
      font-size: 6vw;
    }
  }
</style>
