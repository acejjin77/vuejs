<script setup>
import { ref } from 'vue';
import ResultSearch from './components/result/ResultSearch.vue';

const drawer = ref(null)
</script>


<template>
  <v-app id="inspire">
    <v-navigation-drawer v-model="drawer">

    </v-navigation-drawer>

    <v-app-bar>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-app-bar-title>SCL 결과조회</v-app-bar-title>
    </v-app-bar>

    <v-main>
      <ResultSearch></ResultSearch>
    </v-main>
  </v-app>
</template>