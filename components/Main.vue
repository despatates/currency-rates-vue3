<template>
  <v-row class="currency-converter">
    <v-col cols="12" class="text-center">
      <h3>Convertisseur de devises</h3>
      <div class="convertisseur">
      <v-text-field 
        v-model="amount" 
        label="Montant" 
        type="number" 
        class="montant" 
        outlined 
        />
      <v-select
        v-model="fromCurrency"
        :items="currencyOptions"
        label="De"
        class="de"
        outlined
      />
      <v-icon
        @click="swapCurrencies"
        class="icon-change"
        color="black"
      >
        mdi-swap-horizontal
      </v-icon>
      <v-select
        v-model="toCurrency"
        :items="currencyOptions"
        label="Vers"
        class="vers"
        outlined
      />
    </div>
      <div v-if="result" class="conversion-result mt-3">
        <h4>{{ formattedResult }}</h4>
        <p>{{ currentDate }}</p>
      </div>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data() {
    return {
      amount: 1,
      fromCurrency: 'EUR',
      toCurrency: 'USD',
      exchangeRates: null,
      result: null,
    };
  },
  computed: {
    currencyOptions() {
      return Object.keys(this.exchangeRates || {}).map(currency => currency.toUpperCase());
    },
    formattedResult() {
      return `${this.amount.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${this.fromCurrency} = ${this.result.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${this.currencyName(this.toCurrency)}`;
    },
    currentDate() {
      return new Date().toLocaleString('fr-FR', { year: 'numeric', month: '2-digit', day: '2-digit', hour: '2-digit', minute: '2-digit' });
    }
  },
  async mounted() {
    await this.fetchExchangeRates();
    this.convertCurrency();
  },
  methods: {
    async fetchExchangeRates() {
      try {
        const response = await fetch('https://www.floatrates.com/daily/eur.json');
        if (!response.ok) {
          throw new Error('Failed to fetch exchange rates');
        }
        this.exchangeRates = await response.json();
      } catch (error) {
        console.error('Error fetching exchange rates:', error);
      }
    },
    convertCurrency() {
      if (!this.exchangeRates) return;
      
      const rateFromEuro = this.fromCurrency === 'EUR' 
        ? 1 
        : this.exchangeRates[this.fromCurrency.toLowerCase()]?.rate || 1;
      
      const rateToEuro = this.toCurrency === 'EUR' 
        ? 1 
        : this.exchangeRates[this.toCurrency.toLowerCase()]?.rate || 1;
      
      const conversionRate = rateToEuro / rateFromEuro;
      this.result = (this.amount * conversionRate).toFixed(2);
    },
    swapCurrencies() {
      const temp = this.fromCurrency;
      this.fromCurrency = this.toCurrency;
      this.toCurrency = temp;
      this.convertCurrency();
    },
    currencyName(currencyCode) {
      const currencyNames = {
        EUR: 'Euro',
        USD: 'U.S. Dollar',
        GBP: 'Pound Sterling',
        // Ajoutez d'autres devises si nécessaire
      };
      return currencyNames[currencyCode] || currencyCode;
    }
  },
  watch: {
    amount() {
      this.convertCurrency();
    },
    fromCurrency() {
      this.convertCurrency();
    },
    toCurrency() {
      this.convertCurrency();
    }
  }
};
</script>

<style scoped>

.convertisseur {
  display: flex;
  align-items: center;

}
.currency-converter {
  border: 2px solid black;
  padding: 20px;
  max-width: 500px;
  margin: 50px auto;
  font-family: 'Courier New', Courier, monospace;
  background-color: #f8f8f8;
}

.conversion-result {
  font-size: 1.5em;
  font-weight: bold;
  margin-top: 20px;
}

h4 {
  margin: 0;
}

.icon-change {
  font-size: 2em;
  cursor: pointer;
  border: 2px solid black;
  padding: 10px;
}

.montant {
  width: 50px;
  padding: 20px;
  
}

.de {
  width: 50px;
  padding: 20px;
}

.vers {
  width: 50px;
  padding: 20px;
}

</style>
