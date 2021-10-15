<template>
  <div id="app">
    <main v-if="!loading && currencies.length > 0">
      <div class="info">
        <div>
          <div class="low">
            {{ coin.codein }} {{ coin.low }}
          </div>
          <div class="high">
            {{ coin.codein }} {{ coin.high }}
          </div>
        </div>
        <h3>{{ coin.name }}</h3>

        <div class="converter">
          <input @input="leftToRight" type="number" step="0.50" v-model="leftValue">
          <h2>=</h2>
          <input @input="rightToLeft" type="number" step="0.50" v-model="rightValue">
        </div>
      </div>

      <div class="currencies">
        <a 
          v-for="currency in currencies" 
          :key="currency.code + currency.codein" 
          class="currency"
          @click="changeCoin(currency)"
        >
          {{ currency.code }}
        </a>
      </div>

      <small>Powered by <a href="https://docs.awesomeapi.com.br">AwesomeAPI</a></small>
    </main>

    <div class="loading" v-if="loading">
      <img src="assets/loading.svg" width="50">
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

type CurrencyObject = {
  code: string;
  codein: string;
  name: string;
  high: string;
  low: string;
  varBid: string;
  pctChange: string;
  bid: string;
  ask: string;
  timestamp: string;
  create_date: string;
}

export default Vue.extend({
  data(){
    return {
      API_BASE: "https://economia.awesomeapi.com.br/json",
      currencies: [] as CurrencyObject[],
      coin: {} as CurrencyObject,
      loading: false,
      leftValue: 1.00,
      rightValue: 1.00
    }
  },
  created(){
    this.getCurrencies()
  },
  methods: {
    getCurrencies(){
      this.loading = true;
      fetch(`${this.API_BASE}/all`)
      .then(response => response.json())
      .then(response => {
        this.currencies = Object.values(response)
        this.coin = this.currencies[0]
        this.rightValue = Number(this.coin.bid)
      })
      .catch(() => alert("Error on load currencies, try again."))
      .finally(() => this.loading = false)
    },
    changeCoin(currency: CurrencyObject){
      this.coin = currency;
      this.leftValue = 1;
      this.rightValue = Number(currency.bid)
    },
    leftToRight(){
      this.rightValue = this.fix(Number(this.coin.bid) * this.leftValue)
    },
    rightToLeft(){
      this.leftValue = this.fix(this.rightValue / Number(this.coin.bid))
    },
    fix(num: number){
      return Number(num.toFixed(2))
    }
  }
})
</script>

<style>
:root {
  --white: #F2F3D9;
  --dark-blue: #151E3F;
  --black: #030027;
  --orange: #DC9E82;
  --pink: #C16E70;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
}

body {
  background-color: var(--dark-blue);
}

.loading {
  width: 100vw;
  height: 100vh;
  justify-content: center;
  align-items: center;
  display: flex;
  flex-direction: column;
}

main {
  display: flex;
  width: 100vw;
  height: 100vh;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  color: var(--white)
}

a {
  transition: .3s;
  color: var(--white)
}

a:hover {
  color: var(--pink)
}

.info {
  display: flex;
  width: 100vw;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.info > div {
  display:  flex;
  margin-bottom: 10pt;
}

.info .high, .info .low {
  font-size: 24pt;
  font-weight: 500;
  color: red
}

.info .high {
  color: green;
  margin-left: 20pt;
}

.currencies {
  display: grid;
  background-color: var(--black);
  width: 350pt;
  margin: 30pt auto;
  grid-template-columns: 1fr 1fr 1fr 1fr;
}

.currencies .currency {
  transition: .3s;
  padding: 10pt;
  flex: 0 1;
  text-align: center;
  cursor: pointer;
}

.currencies .currency:hover {
  background-color: var(--pink);
  color: var(--dark-blue)
}

.converter input{
  outline: none;
  border: none;
  padding: 10pt;
  background-color: var(--pink);
  font-size: 15pt;
  text-align: center;
  width: 150pt;
  margin: 10pt;
}

.converter h2{
  display: flex;
  align-items: center;
}
</style>
