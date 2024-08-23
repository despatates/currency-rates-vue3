<template>
  <NuxtLayout>
    <v-app>
      <v-main>
        <v-container class="custom-width fill-height">
          <NuxtPage />
        </v-container>
      </v-main>
    </v-app>
  </NuxtLayout>
</template>
<style scoped>

.custom-width {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
