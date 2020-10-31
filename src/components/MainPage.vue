<template>
  <div class="mainpage">
    <div class="currencies">
      <div class="name">Compare currencies</div>
      <div class="lists">
        <div class="list">
          <button v-for="cur in currencyList"
                  :key="cur"
                  ref="divF"
                  class="first-list__btn"
                  @click="chooseMain(cur)"
          >{{ cur }}</button>
        </div>
        <div class="list reverse">
          <button v-for="cur in currencyList"
                  :key="cur"
                  ref="divS"
                  class="second-list__btn"
                  @click="chooseAdditional(cur)"
          >{{ cur }}</button>
        </div>
        <div class="second-list"></div>
      </div>
    </div>
      <div class="watchfield">
        <div class="period-btn" v-show="isCurrenciesSelected">
            <div class="period-btn__panel">
                <div class="current-price">Current price: {{ info }}</div>
                <div class="period-btn__buttons">
                    <button v-for="el in period"
                            :key="el"
                            ref="period"
                            class="period-btn__button"
                            @click="createOpebCloseList(el)"
                    >{{ el.slice(5, el.length - 1).toUpperCase() }}
                    </button>
                </div>
            </div>
            <div class="period-btn__current">
                    <Current />
            </div>
        </div>
      <div class="graph" v-show="switcher">
          <Graph />
      </div>
      </div>
  </div>
</template>

<script>
import Graph from './Graph'
import Current from './Current'

export default {
  name: 'MainPage',
    props: {
      period: Array,
      info: Number,
    },
  components: {
    Graph, Current
  },
  data() {
    return {
      currencyList: ['USD', 'EUR', 'JPY', 'BTC', 'ETH', 'LTC', 'ALGO', 'XRP', 'EOS', 'TRX', 'UNI', 'ADA', 'DOT', 'NEO', 'FIL'],
      firstC: 'fsym=',
      secondC: '&tsym=',
      switcher: false,
      preInner: true
    }
  },
    computed: {
      isCurrenciesSelected: function () {
          return this.firstC.length > 5 && this.secondC.length > 7;
      }
    },
  methods: {
    chooseMain(cur) {
      this.firstC = this.firstC.slice(0, 5).concat(cur);
      this.$refs.divF.forEach(el => {
        el.classList.remove('chosen')
        if(el.innerText === String(cur)) el.classList.add('chosen')
      });
        this.$refs.divS.forEach(el => {el.disabled = false; el.classList.remove('disabled')});
        this.$refs.divS.filter(el => el.innerText === cur).forEach(el => {el.disabled = true; el.classList.add('disabled')});
      this.switcher = false;
      this.$children[0]._data.charData.datasets[0].data = [];
    },
    chooseAdditional(cur) {
      this.secondC = this.secondC.slice(0, 6).concat(cur);
      this.$refs.divS.forEach(el => {
        el.classList.remove('chosen')
        if(el.innerText === String(cur)) el.classList.add('chosen')
      });
      this.switcher = false;
      this.preInner = false;
      this.$children[0]._data.charData.datasets[0].data = [];
    },
    createOpebCloseList(el) {
      let Topparent = this.$root.$children[0]._data;
      let child = this.$children[1]._data;
      if(Topparent.end.length >=17) {
          fetch(Topparent.api + Topparent.period[Topparent.period.indexOf(el)] + Topparent.end + Topparent.limit)
              .then(data => data.json())
              .then(json => {
                  let newCharData = {...child.charData}
                  newCharData.datasets[0].data = [];
                  newCharData.datasets[1].data = [];
                  for (let el in json.Data.Data) {
                      newCharData.datasets[0].data.push([String(json.Data.Data[el].open), String(json.Data.Data[el].close)]);
                      newCharData.datasets[1].data.push([String(json.Data.Data[el].low), String(json.Data.Data[el].high)]);
                  }
                  child.charData = {...newCharData}

                  })
              .then(this.switcher = true)
              .then(
                  this.$refs.period.forEach(item => {
                      item.classList.remove('chosen-period');
                  if(item.innerText.replace(/[^A-Za-z]/g, "") === el.slice(5, el.length - 1).toUpperCase()) {
                      item.classList.add('chosen-period');
                  }
              }))
              .catch(err => console.log(err))
              }
        }
      },
  watch: {
    firstC() {
      if(this.firstC.length > 5) this.$parent._data.end = this.firstC + this.secondC
        this.createOpebCloseList('histoday?')
    },
    secondC() {
      if(this.secondC.length > 6) {
          this.$parent._data.end = this.firstC + this.secondC;
          this.$parent._data.currency = this.secondC.slice(6, this.secondC.length);
      }
      this.createOpebCloseList('histoday?');
    }
  },
  mounted() {
      this.chooseMain('BTC');
      this.chooseAdditional('USD');
      if(this.secondC.length > 6) {
          this.$parent._data.end = this.firstC + this.secondC;
          this.$parent._data.currency = this.secondC.slice(6, this.secondC.length);
      }
      this.createOpebCloseList('histoday?');
  }
}
</script>

<style scoped>
  .mainpage {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 2vw;
    width: 70%;
    /*min-width: 800px;*/
    height: 90vh;
    /*min-height: 500px;*/
    border-radius: 20px;
    border: 1px solid grey;
  }

  .currencies {
      width: 15%;
  }

  .watchfield {
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      align-content: flex-start;
      width: 70%;
      height: 100%;
  }

  .name {
      font-weight: bold;
  }

  .lists {
    display: flex;
  }

  .list {
    display: flex;
    flex-direction: column;
    margin-right: 1vw;
  }

  .reverse {
    flex-direction: column-reverse;
  }

  .first-list__btn {
    background-color: rgba(255, 159, 101, 0.84);
    width: 70px;
    border-radius: 5px;
    margin-bottom: 5px;
    border: none;
    padding: 1vw;
    color: #a56516;
    transition: background-color 0.2s;
    cursor: pointer;
  }

  .first-list__btn:hover {
    background-color: rgba(255, 159, 101, 0.52);

  }

  .second-list__btn {
    background-color: rgba(159, 255, 150, 0.84);
    width: 70px;
    border-radius: 5px;
    margin-bottom: 5px;
    border: none;
    padding: 1vw;
    color: #34ad35;
    transition: background-color 0.2s;
    cursor: pointer;
  }

  .second-list__btn:hover {
    background-color: rgba(0, 229, 38, 0.44);
  }

  .chosen {
    background-color: rgba(122, 122, 122, 0.8);
  }

  .disabled {
      background-color: rgba(211, 211, 211, 0.33);
      color: lightgrey;
  }

  .graph {
      display: flex;
      flex-grow: 1;
      width: 100%;
  }

  .period-btn {
      display: flex;
      justify-content: center;
      align-items: center;
  }

  .period-btn__panel {
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      height: 70%;
  }

  .current-price {
      font-weight: bold;
  }

  .period-btn__buttons {
      display: flex;
      flex-direction: row;
  }

  .period-btn__button {
      padding: 1.5vw 1vw 1.5vw 1vw;
      background-color: black;
      color: white;
      margin-right: 1vw;
      border-radius: 10px;
      height: max-content;
  }

  .chosen-period {
      color: black;
      background-color: white;
  }

  .period-btn__current {
      width: 200px;
  }

  .pre-inner {
      display: flex;
      justify-content: center;
      align-items: center;
      background-color: white;
      width: 85%;
      height: 100%;
  }
</style>
