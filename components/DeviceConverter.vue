<template>
  <div class="converter">
    <v-text-field
      v-model="internalAmount"
      label="Montant"
      type="number"
      class="amount"
      :rules="[v => v >= 0 ]"
      outlined
      @input="updateAmount"
    />
    <v-select
      v-model="internalFromCurrency"
      :items="filteredFromCurrencyOptions"
      label="De"
      class="de"
      outlined
      @change="onFromCurrencyChange"
    />
    <v-btn @click="swapCurrencies" class="icon-change" color="white" icon>
      <v-icon>mdi-swap-horizontal</v-icon>
    </v-btn>
    <v-select
      v-model="internalToCurrency"
      :items="filteredToCurrencyOptions"
      label="Vers"
      class="vers"
      outlined
      @change="onToCurrencyChange"
    />
  </div>
</template>

<script setup>
import { ref, defineEmits, watch, computed } from 'vue';

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

const emit = defineEmits(['updateAmount', 'updateFromCurrency', 'updateToCurrency', 'swapCurrencies']);

const internalAmount = ref(props.amount);
const internalFromCurrency = ref(props.fromCurrency);
const internalToCurrency = ref(props.toCurrency);

//Computed properties
const filteredFromCurrencyOptions = computed(() => {
  return props.currencyOptions.filter(currency => currency !== internalToCurrency.value);
});

const filteredToCurrencyOptions = computed(() => {
  return props.currencyOptions.filter(currency => currency !== internalFromCurrency.value);
});

watch([internalFromCurrency, internalToCurrency], () => {
  emit('updateFromCurrency', internalFromCurrency.value);
  emit('updateToCurrency', internalToCurrency.value);
});
//Function to update the amount
function updateAmount(event) {
  let newValue = parseFloat(event.target.value);
  if (newValue < 0) {
    newValue = 0;
  }
  internalAmount.value = newValue;
  emit('updateAmount', internalAmount.value);
}

function onFromCurrencyChange() {
  emit('updateFromCurrency', internalFromCurrency.value);
}

function onToCurrencyChange() {
  emit('updateToCurrency', internalToCurrency.value);
}
//Function to exchange currencies
function swapCurrencies() {
  [internalFromCurrency.value, internalToCurrency.value] = [internalToCurrency.value, internalFromCurrency.value];
  emit('swapCurrencies');
}
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
