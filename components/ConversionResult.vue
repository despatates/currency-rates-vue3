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

<script setup>
import { computed } from 'vue';
import { defineProps } from 'vue';

// Define props with specific types and default values
const props = defineProps({
  amount: {
    type: Number,
    required: true
  },
  result: {
    type: Number,
    required: true
  },
  fromCurrency: {
    type: String,
    required: true
  },
  toCurrency: {
    type: String,
    required: true
  },
  currentDate: {  // Ajout de currentDate dans les props
    type: String,
    required: true
  },
});

console.log(props.fromCurrency, props.toCurrency);

// Function to get currency name
function getCurrencyName(currencyCode) {
  console.log("currency code:", currencyCode);
  return new Intl.DisplayNames(['fr'], { type: 'currency' }).of(currencyCode);
}

// Computed property for formatted result
const formattedResult = computed(() => {
  const amount = props.amount ? parseFloat(props.amount) : 0;
  const result = props.result ? parseFloat(props.result) : 0;
  return `${amount.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${getCurrencyName(props.fromCurrency)} = ${result.toLocaleString('fr-FR', { minimumFractionDigits: 2 })} ${getCurrencyName(props.toCurrency)}`;
});
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
