<template>
    <div class="table">
      <h2>Historique des conversions</h2>
      <v-data-table
        :headers="headers"
        :items="conversionHistory"
        class="elevation-1"
      >
        <!-- Personnalisation de l'affichage des colonnes via des slots -->
        <template v-slot:[`item.date`]="{ item }">
          {{ item.date }}
        </template>
        <template v-slot:[`item.amount`]="{ item }">
          {{ item.amount }}
        </template>
        <template v-slot:[`item.fromCurrency`]="{ item }">
          {{ item.fromCurrency }}
        </template>
        <template v-slot:[`item.toCurrency`]="{ item }">
          {{ item.toCurrency }}
        </template>
        <template v-slot:[`item.result`]="{ item }">
          {{ item.result }}
        </template>
      </v-data-table>
    </div>
  </template>
  
  <script setup>
  const headers = [
    { text: 'Date', value: 'date' },
    { text: 'Montant', value: 'amount' },
    { text: 'De', value: 'fromCurrency' },
    { text: 'Vers', value: 'toCurrency' },
    { text: 'Résultat', value: 'result' },
  ]
  
  const props = defineProps({
    conversionHistory: {
      type: Array,
      required: true,
    }
  });
  </script>
  
  <style scoped>
  .table {
    padding: 10px;
    text-align: center;
    width: 100%;
  }
  
  h2 {
    margin-bottom: 20px;
  }
  </style>
  