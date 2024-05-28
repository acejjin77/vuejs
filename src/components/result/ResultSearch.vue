<script setup>
import { computed, ref } from 'vue';
import axios from 'axios';

// JSON 데이터 호출 전 데이터 체킹 및 구성
const dateFrom = ref('');
const dateTo = ref('');
const sclResultList = ref([]);

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

const loading = ref(false)

function load () {
  loading.value = true
  setTimeout(() => (loading.value = false), 3000)
}

// JSON 데이터 호출 부분
const axiosConfig = {
  headers: {
        'Content-Type': 'application/json',
        'x-api-key' : 'suPkjx7bwpmePaiqKXoOkQ=='
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

// v-data-table 설정
const noResultText = '자료가 없습니다.';
const itemsPerPageTxt = '';
const search = ref('');
const tableHeaders = [
  {title: '성명', value: 'PNAME'},
  {title: '차트번호', value: 'CHARTNO'},
  {title: '검사코드', value: 'HITEMCODE'},
  {title: '검사명', value: 'HITEMNAME'},
  {title: '보험코드', value: 'INSUCODE'},
  {title: '접수일자', value: 'SCLORDDATE'},
  {title: '접수번호', value: 'SCLORDNO'},
]
const selectedPatient =ref(null);

function showPatient(event, {item}) {
  selectedPatient.value = item
}
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
            class="mr-4"
            required
          ></v-text-field>
        </v-col>
        <v-col>
          <v-text-field
            v-model="dateTo"
            :counter="8"
            label="To"
            :rules="dateRules"
            class="mr-4"
            required
          ></v-text-field>
        </v-col>
        <v-col>
          <v-btn
            outlined
            color="blue"
            elevation="8"
            class="text-none mt-6"
            type="submit"
            @click="load">
            결과조회
          </v-btn>
        </v-col>
        <v-spacer></v-spacer>
        <v-spacer></v-spacer>
        <v-spacer></v-spacer>
      </v-row>
    </v-form>

    <v-row>
      <v-col>
        <v-data-table
          :search="search"
          :items="sclResultList"
          :no-data-text="noResultText"
          :items-per-page-text="itemsPerPageTxt"
          :loading="loading"
          loading-text="환자 리스트 로딩 중..."
          :headers="tableHeaders"
          @click:row="showPatient"
          select-strategy="single"
          :mobile="false"
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
      </v-col>
      <v-col>
        <div v-if="selectedPatient != null">
          <v-row>
            <v-col>
              환자이름
            </v-col>
            <v-col>
              차트번호
            </v-col>
            <v-col>
              바코드번호
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              {{ selectedPatient.PNAME }}
            </v-col>
            <v-col>
              {{ selectedPatient.CHARTNO }}
            </v-col>
            <v-col>
              {{ selectedPatient.HBARCODE }}
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              결과 : 
            </v-col>
            <v-col cols="8">
              {{ selectedPatient.RESULT }}
            </v-col>
            <v-spacer>
            </v-spacer>
          </v-row>
          <v-row>
            <v-col>
              서술형결과 : 
            </v-col>
            <v-col cols="10">
              {{ selectedPatient.FRESULT }}
            </v-col>
          </v-row>
        </div>
      </v-col>
    </v-row>
    

  </v-container>
</template>
