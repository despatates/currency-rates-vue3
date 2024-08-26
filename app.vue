<template>
  <NuxtLayout>
    <v-app :class="[darkTheme ? 'dark-theme' : 'light-theme']">
      <v-main>
        <v-container class="custom-width fill-height">
          <NuxtPage />
        </v-container>
      </v-main>
    </v-app>
  </NuxtLayout>
</template>

<script setup>
import { ref, provide } from 'vue';

const darkTheme = ref(false);
provide('darkTheme', darkTheme);
</script>

<style scoped>
.dark-theme {
  background-color: #333;
  color: #fff;
}

.light-theme {
  background-color: #f4f4d9;
  color: #000;
}

.custom-width {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
