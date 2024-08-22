<template>
  <v-row class="currency-converter">
    <v-col cols="12" class="text-center">
      <div class="d-flex">
        <img src="../docs/logo-cd.png" alt="logo-accueil">
        <h3>Convertisseur de devises</h3>
      </div>
      <div class="convertisseur">
        <v-text-field v-model="amount" label="Montant" type="number" class="montant" outlined />
        <v-select v-model="fromCurrency" :items="currencyOptions" label="De" class="de" outlined />
        <v-icon @click="swapCurrencies" class="icon-change" color="black">
          mdi-swap-horizontal
        </v-icon>
        <v-select v-model="toCurrency" :items="currencyOptions" label="Vers" class="vers" outlined />
      </div>
      <div v-if="result" class="conversion-result mt-3">
        <h4>{{ formattedResult }}</h4>
        <p>{{ currentDate }}</p>
      </div>
    </v-col>
  </v-row>
  <div class="mt-5">
    <h4>Historique des conversions</h4>
    <v-simple-table>
      <thead>
        <tr>
          <th>Date</th>
          <th>Montant</th>
          <th>De</th>
          <th>Vers</th>
          <th>Résultat</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(conversion, index) in conversionHistory" :key="index">
          <td>{{ conversion.date }}</td>
          <td>{{ conversion.amount }}</td>
          <td>{{ conversion.fromCurrency }}</td>
          <td>{{ conversion.toCurrency }}</td>
          <td>{{ conversion.result }}</td>
        </tr>
      </tbody>
    </v-simple-table>
  </div>
</template>

<script>
export default {
  // Propriétés de l'objet
  data() {
    return {
      amount: 1,
      fromCurrency: 'EUR',
      toCurrency: 'USD',
      exchangeRates: null,
      result: null,
      currentDate: this.getCurrentDate(), // Initialise avec la date et l'heure actuelles
      conversionHistory: [] // Tableau pour stocker l'historique des conversions
    };
  },
  // Propriétés calculées de l'objet
  computed: {
    // Retourne les options de devise disponibles pour la conversion
    currencyOptions() {
      return Object.keys(this.exchangeRates || {}).map(currency => currency.toUpperCase());
    },
    // Retourne le résultat de la conversion de devise formaté
    formattedResult() {
      return `${this.amount.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${this.fromCurrency} = ${this.result.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${this.currencyName(this.toCurrency)}`;
    },
  },
  // Cycle de vie de l'objet
  async mounted() {
    await this.fetchExchangeRates();
    this.convertCurrency();
  },
  // Méthodes de l'objet
  methods: {
    // Retourne la date et l'heure actuelles
    //replace permet de changer le format de la date
    getCurrentDate() {
    return new Date().toLocaleString('fr-FR', {
      //détailler l'affichage pour retirer les secondes
      year: 'numeric',
      month: '2-digit',
      day: '2-digit',
      hour: '2-digit',
      minute: '2-digit'
    }).replace(/(\d{2})\/(\d{2})\/(\d{4})/, '$3/$2/$1');
  },
    // Récupère les taux de change
    async fetchExchangeRates() {
      try {
        const response = await fetch('https://www.floatrates.com/daily/eur.json');
        if (!response.ok) {
          throw new Error('Failed to fetch exchange rates');
        }// Stocke les taux de change dans l'objet exchangeRates
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

      // Ajouter la conversion à l'historique
      this.conversionHistory.push({
        date: this.currentDate,
        fromCurrency: this.fromCurrency,
        toCurrency: this.toCurrency,
        amount: this.amount,
        result: this.result
      });
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
  // Surveille les changements des propriétés de l'objet
  watch: {
    amount(newVal, oldVal) {
      if (newVal !== oldVal) {
        this.convertCurrency();
      }
    },
    fromCurrency(newVal, oldVal) {
      if (newVal !== oldVal) {
        this.convertCurrency();
      }
    },
    toCurrency(newVal, oldVal) {
      if (newVal !== oldVal) {
        this.convertCurrency();
      }
    }
  }
};
</script>

<style scoped>
img {
  width: 150px;
  height: 150px;
  margin-right: 10px;
}

h3 {
  display: flex;
  font-size: 2rem;
  align-items: center;
}

.convertisseur {
  display: flex;
  align-items: center;
  border-radius: 10px;
  background: linear-gradient(90deg, rgba(255, 4, 60, 1) 56%, rgba(255, 129, 10, 1) 100%);
}

.currency-converter {
  border: 4px solid rgb(239, 119, 0);
  border-radius: 10px;
  padding: 20px;
  max-width: 800px;
  margin: 5px auto;
  font-family: 'Courier';
  background-color: #f4f4d9;
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
  border-radius: 10px;
  font-size: 2em;
  cursor: pointer;
  border: 2px solid rgb(255, 255, 255);
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
