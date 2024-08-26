<template>
  <v-row :class="[darkTheme ? 'dark-theme' : 'light-theme', 'currency-converter']">
    <v-col cols="12" class="text-center pb-0">
      <div class="d-flex">
        <img src="../docs/logo-cd.png" alt="logo-accueil">
        <h1>Convertisseur de devises</h1>
      </div>
      <div class="converter">
        <DeviceConverter
        :amount="amount"
        :fromCurrency="fromCurrency"
        :toCurrency="toCurrency"
        :currencyOptions="currencyOptions"
        @updateAmount="amount = $event"
        @updateFromCurrency="fromCurrency = $event"
        @updateToCurrency="toCurrency = $event"
        @swapCurrencies="swapCurrencies"
      />
      </div>
      <ConversionResult :amount="amount" :result="result" :fromCurrency="fromCurrency" :toCurrency="toCurrency"
        :currentDate="currentDate" />
    </v-col>
    <v-col cols="12" class="switch-theme">
      <v-switch v-model="darkTheme" label="Clair/Sombre" class="mt-4"></v-switch>
    </v-col>
  </v-row>
  <ConversionHistory :conversionHistory="conversionHistory" />
</template>

<script>
import { inject } from 'vue';
import ConversionResult from './ConversionResult.vue';
import DeviceConverter from './DeviceConverter.vue';


export default {
  setup() {
    const darkTheme = inject('darkTheme');
    return {
      darkTheme,
    };
  },
  // Object Properties
  data() {
    return {
      amount: 1,
      fromCurrency: 'EUR',
      toCurrency: 'USD',
      exchangeRates: null,
      result: 0,
      currentDate: this.getCurrentDate(), // Initialize with the current date and time
      conversionHistory: [], // Table to store conversion history
      initialized: false, // Flag to skip first conversion
    };
  },
  // Calculated properties of the object
  computed: {
    // Returns the currency options available for conversion
    currencyOptions() {
      return Object.keys(this.exchangeRates || {}).map(currency => currency.toUpperCase()).sort();
    },

  },
  // Object life cycle
  async mounted() {
    await this.fetchExchangeRates();
    this.convertCurrency();
  },
  // Object Methods
  methods: {
    // Returns the current date and time
    //replace allows you to change the date format
    getCurrentDate() {
      return new Date().toLocaleString('fr-FR', {
        //detail the display to remove the seconds
        year: 'numeric',
        month: '2-digit',
        day: '2-digit',
        hour: '2-digit',
        minute: '2-digit'
      }).replace(/(\d{2})\/(\d{2})\/(\d{4})/, '$3/$2/$1');
    },
    // Get exchange rates
    async fetchExchangeRates() {
      try {
        const response = await fetch('https://www.floatrates.com/daily/eur.json');
        if (!response.ok) {
          throw new Error('Failed to fetch exchange rates');
        }// Stores exchange rates in the exchangeRates object
        this.exchangeRates = await response.json();
        // Adds the euro to euro exchange rate
        this.exchangeRates['eur'] = { rate: 1 };
      } catch (error) {
        console.error('Error fetching exchange rates:', error);
      }
    },
    // Converts the amount from the source currency to the destination currency
    convertCurrency() {
      if (!this.exchangeRates) return;
      // Exchange rate for the starting currency
      const rateFromEuro = this.fromCurrency === 'EUR'
        ? 1
        : this.exchangeRates[this.fromCurrency.toLowerCase()]?.rate || 1;
      // Exchange rate for arrival currency
      const rateToEuro = this.toCurrency === 'EUR'
        ? 1
        : this.exchangeRates[this.toCurrency.toLowerCase()]?.rate || 1;
      // Calculating the exchange rate
      const conversionRate = rateToEuro / rateFromEuro;
      this.result = Math.round((this.amount * conversionRate) * 100) / 100; // Rounded to 2 decimal places as a number
      this.updateCurrentDate(); // Updates date and time after conversion



      // If the component is not yet initialized, the first conversion is ignored.
      if (!this.initialized) {
        this.initialized = true;
        return; // We exit the method without adding the conversion to the history
      }
      // Add to history only if result is greater than 0
      if (parseFloat(this.result) > 0) {
        this.conversionHistory.push({
          date: this.currentDate,
          fromCurrency: this.fromCurrency,
          toCurrency: this.toCurrency,
          amount: this.amount,
          result: this.result
        });
      }
    },
    // Reverses the source and destination currencies
    swapCurrencies() {
      const temp = this.fromCurrency;
      this.fromCurrency = this.toCurrency;
      this.toCurrency = temp;
      this.convertCurrency();
    },

    // Method to switch between light mode and dark mode
    toggleDarkMode() {
      this.$vuetify.theme.dark = !this.$vuetify.theme.dark;
    },
    // Updates the current date and time
    updateCurrentDate() {
      this.currentDate = this.getCurrentDate();
    },
    // Updates the amount and performs the conversion
     updateAmount(newAmount) {
      this.amount = newAmount;
      this.convertCurrency();
    },
  },
  // Monitors changes in object properties
  watch: {
    amount(newVal, oldVal) {
      if (newVal < 0) {
        this.amount = 0;
        // Resets to zero if the value is negative
      } else if (newVal !== oldVal) {
        this.convertCurrency();
        // Performs the conversion if the value is valid
      }
    },
  }
};
</script>

<style scoped>
img {
  width: 150px;
  height: 150px;
  margin-right: 10px;
}

h1 {
  display: flex;
  font-size: 2rem;
  align-items: center;
}



.currency-converter {
  border: 4px solid rgb(239, 119, 0);
  border-radius: 10px;
  padding-left: 20px;
  padding-right: 20px;
  max-width: 800px;
  font-family: 'Courier';
  background-color: #f4f4d9;
  margin-bottom: 30px;
  margin-top: 30px;
}

.dark-theme {
  background-color: #333;
  color: #fff;
}

.light-theme {
  background-color: #f4f4d9;
  color: #000;
}

.conversion-result {
  font-size: 1.5em;
  font-weight: bold;
  margin-top: 20px;
}


h4 {
  margin: 0;
  text-align: center;
}

.icon-change {
  border-radius: 10px;
  font-size: 1.9em;
  cursor: pointer;
  border: 2px solid rgb(255, 255, 255);
  padding: 10px;
}

.switch-theme {
  padding: 0;
}
</style>
