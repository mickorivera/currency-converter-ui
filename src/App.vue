<template>
  <div id="app" class="container my-3">
    <h3>Currency Exchange</h3>

    <div class="card mt-3">
      <div class="card-body">
        <div class="input-group input-group-sm">
          <input v-model="value" @change="onDataChange($event)" />
          <select name="sourceCurrency" @change="onDataChange($event)" class="form-control" v-model="sourceCurrency">
              <option v-for="currency in currencies" :key="currency.code">{{ currency["code"] }}</option>
          </select>
          ->
          <select name="sourceCurrency" @change="onDataChange($event)" class="form-control" v-model="targetCurrency">
              <option v-for="currency in currencies" :key="currency.code">{{ currency["code"] }}</option>
          </select>
          <input type="date" @change="onDataChange($event)" v-model="targetDate"/>
        </div>

        <div v-if="getResult" class="alert alert-secondary mt-2" role="alert"><pre>{{getResult}}</pre></div>
      </div>
    </div>

  </div>
</template>

<script>
import http from "./http-common";

export default {
  name: "App",
  data() {
    return {
      value: 1,
      sourceCurrency: null,
      targetCurrency: null,
      targetDate: new Date().toISOString().substr(0, 10),
      currencies: [],
      getResult: null,
      postResult: null,
      putResult: null,
      deleteResult: null,
    }
  },
  mounted () {
      this.getCurrencies()
  },
  methods: {
    fortmatResponse(res) {
      return JSON.stringify(res, null, 2);
    },

    async getCurrencies() {
        try {
            const res = await http.get("/supported-currencies");

            const result = {
                status: res.status + "-" + res.statusText,
                headers: res.headers,
                data: res.data,
            };

            this.currencies = result.data.supported_currencies;
            result.data.supported_currencies.unshift({"code": "PHP"}, {"code": "EUR"}, {"code": "USD"})
            this.sourceCurrency = result.data.supported_currencies[0].code
            this.targetCurrency = result.data.supported_currencies[1].code
        } catch (err) {
            this.currencies = this.fortmatResponse(err.response?.data) || err;
        }
    },

    async onDataChange() {
      try {
        if (this.targetCurrency == null || this.sourceCurrency == null || this.value == 0) {
            this.getResult = null
            return {}
        }
        let [y, m, d] = this.targetDate.split('-');
        const params = new URLSearchParams(
            [
                ["source_currency", this.sourceCurrency],
                ["target_currency", this.targetCurrency],
                ["amount", this.value],
                ["month", m],
                ["day", d],
                ["year", y]
            ]
        );
        const res = await http.get("/exchange-rates", { params } );
        console.log("hereee")

        const result = {
          status: res.status + "-" + res.statusText,
          headers: res.headers,
          data: res.data,
        };
        this.getResult = result.data;
      } catch (err) {
        this.getResult = this.fortmatResponse(err.response?.data.detail[0]) || err;
      }
    },

  }
}
</script>

<style>
#app {
  max-width: 1000px;
  margin: auto;
}
</style>
