<template>
  <div>
    <h1>Конвертер валют</h1>
    <div v-if="loading" class="loader">Loading...</div>
    <div v-else>
      <div v-if="error" class="error-message" style="color: red">Error loading data</div>
      <div v-else>
        <select v-model="selectedCurrency" :disabled="loading" @change="convertCurrency">
          <option v-for="(value, key) in currencies" :key="key" :value="key">
            {{ key }} ({{ value.Name }})
          </option>
        </select>
        <input type="number" v-model="inputAmount" :disabled="loading" @input="convertCurrency" />
        <input type="text" v-model="outputAmount" :disabled="true" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      apiUrl: "https://www.cbr-xml-daily.ru/daily_json.js",
      loading: true,
      error: false,
      currencies: {},
      selectedCurrency: "",
      inputAmount: 0,
      outputAmount: "",
    };
  },
  mounted() {
    this.loadCurrencies();
  },
  methods: {
    loadCurrencies() {
      fetch(this.apiUrl)
        .then((response) => response.json())
        .then((data) => {
          this.loading = false;
          this.currencies = data.Valute;
          const savedCurrency = localStorage.getItem("selectedCurrency");
          if (savedCurrency && this.currencies[savedCurrency]) {
            this.selectedCurrency = savedCurrency;
          } else {
            this.selectedCurrency = Object.keys(this.currencies)[0];
          }
          this.convertCurrency();
        })
    },
    convertCurrency() {
      localStorage.setItem("selectedCurrency", this.selectedCurrency);
      const rate = this.currencies[this.selectedCurrency].Value;
      const inputAmount = parseFloat(this.inputAmount);
      if (!isNaN(inputAmount)) {
        this.outputAmount = (inputAmount * rate).toFixed(2) + " ₽";
      } else {
        this.outputAmount = "";
      }
    },
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  background-color: #f2f2f2;
}

#app {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 60%;
  height: 300px;
  margin: 0 auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
  background: linear-gradient(-45deg, #de6945, #da2f71, #1c97c3, #cf07e9 );
  background-size: 400% 400%;
  -webkit-animation: gradientBG 10s ease infinite;
  animation: gradientBG 10s ease infinite;
}

h1 {
  font-size: 61px;
  font-weight: bold;
  color: #333;
  margin-bottom: 20px;
  color: white;
}

input[type="input"],
select {
  width: 100%;
  max-width: 200px;
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  font-size: 16px;
  margin-bottom: 10px;
}

select {
  background-size: 16px 16px;
  padding-right: 30px;
  max-width: 523px;
}

input[type="input"] {
  text-align: right;
  max-width: 500px;
}

#outputCurrency {
  font-weight: bold;
  max-width: 500px;
  background-color: white;
}

@-webkit-keyframes gradientBG {
  0% {
  background-position: 0% 50%;
  }
  50% {
  background-position: 100% 50%;
  }
  100% {
  background-position: 0% 50%;
  }
}
@keyframes gradientBG {
  0% {
  background-position: 0% 50%;
  }
  50% {
  background-position: 100% 50%;
  }
  100% {
  background-position: 0% 50%;
  }
}
</style>
