<script setup>
import { computed, ref } from 'vue';
import axios from 'axios';

const nowYYYYmmdd = '20240102'
// const nowYYYYmmdd = computed(() => { 
//     const today = new Date();
//     const year = today.getFullYear();
//     let month = today.getMonth() + 1;
//     let day = today.getDate();

//     if (month < 10) {
//       month = '0' + month;
//     }
//     if (day < 10) {
//       day = '0' + day;
//     }

//     return year+month+day;
// })

const dateFrom = ref(null);
const dateTo = ref(null);
const searchKind = ref(null);
const sclResultList = ref(null);
const axiosConfig = {
  headers: {
        'Content-Type': 'application/json',
        'x-api-key' : 'tK2ZOh4nazTNiy20jbCVIw=='
  }
};


async function getSclResult() {
  try {
    
    const requestOptions = {
      result_kind: 1,
      date_kind : 2,
      date_from : dateFrom.value,
      date_to : dateTo.value,
      send_kind : 0
    };

    await axios.post('/scl/ResultApiSvr', requestOptions, axiosConfig)
      .then(r=> {
        sclResultList.value = r.data.PATIENT_LIST;
      }
    );
  } catch (error) {
    console.error('API 호출 에러: ', error);
  }
};

const isFormValid = computed(() => {
  return (dateFrom?.length === 8 && dateTo?.length === 8);
})

const dateRules = [
  value => {
    if (value?.length == 8) {
      return true
    }
    return '8자리 필수'
  }
];
</script>



<template>
  <v-container no-gutters class="">
    <v-form @submit.prevent="getSclResult">
      <v-layout>
        <v-text-field v-model="dateFrom" :rules="dateRules" :counter="8" label="From" required></v-text-field>
        <v-text-field v-model="dateTo" :rules="dateRules" :counter="8" label="To" required></v-text-field>
        <v-btn :disable="isFormValid" class="mt-2" text="결과 조회" type="submit" variant="outlined"></v-btn>
      </v-layout>
    </v-form>
    <v-layout>
      <div v-for="(field, title) in sclResultList">
        <li :key="title">
          <span :class="title"> key : {{ title }}</span><br>
          <span :class="title"> value : {{ field }} </span>
        </li>
      </div>
    </v-layout>
  </v-container>
</template>
