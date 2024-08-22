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
      currentDate: this.getCurrentDate(), // Initialise avec la date et l'heure actuelles
    };
  },
  computed: {
    currencyOptions() {
      return Object.keys(this.exchangeRates || {}).map(currency => currency.toUpperCase());
    },
    formattedResult() {
      return `${this.amount.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${this.fromCurrency} = ${this.result.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${this.currencyName(this.toCurrency)}`;
    },
  },
  async mounted() {
    await this.fetchExchangeRates();
    this.convertCurrency();
  },
  // Méthodes de l'objet
  methods: {
    // Retourne la date et l'heure actuelles
    getCurrentDate() {
      return new Date().toLocaleString('fr-FR', { year: 'numeric', month: '2-digit', day: '2-digit', hour: '2-digit', minute: '2-digit' });
    },
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
    // Convertit le montant de la devise de départ à la devise d'arrivée
    convertCurrency() {
      if (!this.exchangeRates) return;
      // Taux de change pour la devise de départ
      const rateFromEuro = this.fromCurrency === 'EUR' 
        ? 1 
        : this.exchangeRates[this.fromCurrency.toLowerCase()]?.rate || 1;
      // Taux de change pour la devise d'arrivée
      const rateToEuro = this.toCurrency === 'EUR' 
        ? 1 
        : this.exchangeRates[this.toCurrency.toLowerCase()]?.rate || 1;
      // Calcul du taux de change
      const conversionRate = rateToEuro / rateFromEuro;
      this.result = (this.amount * conversionRate).toFixed(2);
      this.updateCurrentDate(); // Met à jour la date et l'heure après la conversion
    },
    // Inverse les devises de départ et d'arrivée
    swapCurrencies() {
      const temp = this.fromCurrency;
      this.fromCurrency = this.toCurrency;
      this.toCurrency = temp;
      this.convertCurrency();
    },
    // Retourne le nom de la devise en fonction de son code
    currencyName(currencyCode) {
      return new Intl.DisplayNames(['fr'], { type: 'currency' }).of(currencyCode);
    },
    // Met à jour la date et l'heure actuelles
    updateCurrentDate() {
      this.currentDate = this.getCurrentDate();
    },
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
  background: linear-gradient(90deg, rgba(255,4,60,1) 56%, rgba(255,129,10,1) 100%);
}
.currency-converter {
  border: 2px solid black;
  padding: 20px;
  max-width: 800px;
  margin: 5px auto;
  font-family: 'Courier New', Courier, monospace;
  background-color: #f9efef;
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
  color: white;
  
}

.de {
  width: 50px;
  padding: 20px;
  color: white;
}

.vers {
  width: 50px;
  padding: 20px;
  color: white;
}

</style>
