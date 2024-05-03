<script setup>
import { ref } from 'vue';
import ResultSearch from './components/result/ResultSearch.vue';

const drawer = ref(null)

</script>


<template>
  <v-app>
      <v-app-bar title="검사결과조회"></v-app-bar>

      <v-navigation-drawer>
        <v-list>
          <v-list-item title="Navigation item-1"></v-list-item>
          <v-list-item title="Navigation item-2"></v-list-item>
        </v-list>
      </v-navigation-drawer>

    <v-main class="" style="min-width: 300px;">
      <ResultSearch></ResultSearch>
    </v-main>
  </v-app>
</template>