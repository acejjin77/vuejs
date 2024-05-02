<script setup>
import { ref } from 'vue';
import axios from 'axios';


const nowYYYYmmdd = '20240102'
// const nowYYYYmmdd = computed(() => { 
//     const today = new Date();
//     const year = today.getFullYear();
//     let month = today.getMonth() + 1;
//     let day = today.getDate();

//     // Add leading zeros if needed
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
        sclResultList.value = r.data;
      }
    );
  } catch (error) {
    console.error('API 호출 에러 : ', error);
  }
};

const isFormValid = ref(false)
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
  <v-layout class="rounded rounded-md">
    <v-app-bar title="SCL 검사결과조회"></v-app-bar>

    <v-navigation-drawer>
      <v-list>
        <v-list-item title="Navigation"></v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-main class="d-flex align-center justify-center" style="min-height: 300px;">
        <v-form @submit.prevent="getSclResult" v-model="isFormValid">
        <v-container>
          <v-row>
            <v-col cols="12" md="6">
              <v-text-field v-model="dateFrom" :rules="dateRules" :counter="8" label="From" required></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
              <v-text-field v-model="dateTo" :rules="dateRules" :counter="8" label="To" required></v-text-field>
            </v-col>
            <v-col>
              <p>{{ dateFrom }}</p>
              <p>{{ dateTo }}</p>
            </v-col>
            <v-col>
              <v-btn disable="!isFormValid" class="mt-2" text="결과 조회" type="submit" variant="outlined"></v-btn>
            </v-col>
          </v-row>
        </v-container>
        <v-container>
        </v-container>
      </v-form>
    </v-main>
    <v-main>
      <div class="card-body">
          <h5>결과 목록</h5>
          <div v-for="(value, id) in sclResultList">
            <ul v-for="(field, title) in value">
              <li :key="title">
                <span :class="title"> key : {{ title }}</span><br>
                <span :class="title"> value : {{ field }} </span>
              </li>
            </ul>
          </div>
        </div>
    </v-main>
  </v-layout>
</template>