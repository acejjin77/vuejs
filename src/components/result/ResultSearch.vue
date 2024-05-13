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

const dateFrom = ref('');
const dateTo = ref('');
const search = ref('')
const sclResultList = ref([]);
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

const headers = ref([
    {
      text: '접수일자',
      align: 'start',
      sortable: true,
      key: 'orddate'
    },
    {
      text: '환자명',
      key: 'pname',
      align: 'end'
    },
    {
      text: '결과',
      key: 'result',
      sortable: false,
      align: 'end'
    }
  ])
</script>



<template>
  <v-container fluid>
    <v-form @submit.prevent="getSclResult">
      <v-row>
        <v-col>
          <v-text-field
            v-model="dateFrom"
            :counter="8"
            label="From"
            :rules="dateRules"
            class="mr-4 mb-4"
            required
          ></v-text-field>
        </v-col>
        <v-col>
          <v-text-field
            v-model="dateTo"
            :counter="8"
            label="To"
            :rules="dateRules"
            class="mr-4 mb-4"
            required
          ></v-text-field>
        </v-col>
        <v-spacer></v-spacer>
      </v-row>
      <v-row>
        <v-btn
          outlined
          color="blue"
          elevation="8"
          class="text-none mb-6"
          type="submit">
          결과조회
        </v-btn>
      </v-row>
    </v-form>

    <v-row>
      <v-data-table
        :search="search"
        :items="sclResultList"
        item-value="name"
        select-strategy="single"
        :mobile="false"
        density="compact"
      >
        <template #top>
          <v-toolbar
            density="compact"
            :elevation="10"
            theme="dark"
            color="primary"
            >
            <v-toolbar-title>SCL 결과 리스트</v-toolbar-title>
            <v-text-field
              v-model="search"
              label="검색"
              prepend-inner-icon="mdi-magnify"
              variant="outlined"
              hide-details
              single-line
            ></v-text-field>
          </v-toolbar>
        </template>

      </v-data-table>
    </v-row>
  </v-container>
</template>
