<template>
  <div class="converter">
    <v-text-field
      v-model="internalAmount"
      label="Montant"
      type="number"
      class="amount"
      :rules="[v => v >= 0 ]"
      outlined
      @input="$emit('updateAmount', internalAmount)"
    />
    <v-select
      v-model="internalFromCurrency"
      :items="currencyOptions"
      label="De"
      class="de"
      outlined
      @change="$emit('updateFromCurrency', internalFromCurrency)"
    />
    <v-icon @click="$emit('swapCurrencies')" class="icon-change" color="white">
      mdi-swap-horizontal
    </v-icon>
    <v-select
      v-model="internalToCurrency"
      :items="currencyOptions"
      label="Vers"
      class="vers"
      outlined
      @change="$emit('updateToCurrency', internalToCurrency)"
    />
  </div>
</template>

<script>
export default {
  props: {
    amount: Number,
    fromCurrency: String,
    toCurrency: String,
    currencyOptions: Array, // List of currency options
  },
  data() {
    return {
      internalAmount: this.amount,
      internalFromCurrency: this.fromCurrency,
      internalToCurrency: this.toCurrency,
    };
  },
  watch: {
    amount(newVal) {
      this.internalAmount = newVal;
    },
    fromCurrency(newVal) {
      this.internalFromCurrency = newVal;
    },
    toCurrency(newVal) {
      this.internalToCurrency = newVal;
    },
  },
};
</script>

<style scoped>
.converter {
  display: flex;
  align-items: center;
  border-radius: 10px;
  background: linear-gradient(90deg, rgba(255, 4, 60, 1) 56%, rgba(255, 129, 10, 1) 100%);
}

.amount, .de, .vers {
  width: 50px;
  padding: 20px;
  color: white;
}

.icon-change {
  border-radius: 10px;
  font-size: 1.9em;
  cursor: pointer;
  border: 2px solid rgb(255, 255, 255);
  padding: 10px;
}
</style>
