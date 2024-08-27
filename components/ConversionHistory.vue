<template>
    <div class="table">
      <h2>Historique des conversions</h2>
      <v-data-table class="table" :items="conversionHistory"></v-data-table>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      conversionHistory: {
        type: Array,
        required: true,
      },
    },
  };
  </script>
  
  <style scoped>
  .table th {
    padding: 20px;
    text-align: center;
  }
  
  .table h2 {
    text-align: center;
    padding-bottom: 20px;
  }
  
  .table {
    width: 100%;
  }
  </style>
  