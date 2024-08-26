<template>
  <v-row class="conversion-result mt-3" v-if="result">
  <v-col cols="12">
    <h4>{{ formattedResult }}</h4>
  </v-col>
  <v-col cols="12">
    <p>{{ currentDate }}</p>
  </v-col>
</v-row>

</template>

<script>

export default {
  props: {
    amount: Number,
    result: Number,
    fromCurrency: String,
    toCurrency: String,
    currentDate: String,
  },
  methods: {

    getCurrencyName(currencyCode) {
      return new Intl.DisplayNames(['fr'], { type: 'currency' }).of(currencyCode);
    },
  },

  computed: {
    // Returns the formatted currency conversion result
    formattedResult() {
      const amount = this.amount ? parseFloat(this.amount) : 0;
      const result = this.result ? parseFloat(this.result) : 0;
      return `${amount.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${this.getCurrencyName(this.fromCurrency)} = ${result.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${this.getCurrencyName(this.toCurrency)}`;
    },
  },
};

</script>



<style scoped>
.conversion-result {
  font-size: 1.5em;
  font-weight: bold;
  margin-top: 20px;
  text-align: center;
}

h4 {
  margin: 0;
}
</style>
