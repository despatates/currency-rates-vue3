<template>
  <div class="converter">
    <v-text-field
      v-model="internalAmount"
      label="Montant"
      type="number"
      class="amount"
      :rules="[v => v >= 0 ]"
      outlined
      @input="$emit('updateAmount', $event)"
    />
    <v-select
      v-model="internalFromCurrency"
      :items="currencyOptions"
      label="De"
      class="de"
      outlined
      @change="$emit('updateFromCurrency', $event)"
    />
    <v-btn @click="$emit('swapCurrencies')" class="icon-change" color="white" icon>
  <v-icon>mdi-swap-horizontal</v-icon>
</v-btn>
    <v-select
      v-model="internalToCurrency"
      :items="currencyOptions"
      label="Vers"
      class="vers"
      outlined
      @change="$emit('updateToCurrency', $event)"
    />
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';

// Define props with specific types and default values
const props = defineProps({
  amount: {
    type: Number,
    required: true,
  },
  fromCurrency: {
    type: String,
    required: true,
  },
  toCurrency: {
    type: String,
    required: true,
  },
  currencyOptions: {
    type: Array,
    required: true,
  },
});

// Local state mirroring props to handle internal updates
const internalAmount = ref(props.amount);
const internalFromCurrency = ref(props.fromCurrency);
const internalToCurrency = ref(props.toCurrency);


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
  border: 2px solid rgb(255, 255, 255);
  cursor: pointer;
  color: white!important;
  background-color: transparent!important;
  }
</style>
