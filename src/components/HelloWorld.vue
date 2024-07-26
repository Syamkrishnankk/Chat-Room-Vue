<template>
  <v-container class="fill-height">
    <v-responsive
      class="align-centerfill-height mx-auto"
      max-width="900"
    >
    <h2 align="center" class="mb-3">Age Calculator</h2>
    <v-row align="center" justify="center">
    <v-alert
    v-if="isAlert"
    title="Invalid user input"
    color="red-darken-4"
    closable
    max-width="400"
    class="ma-5"
  >
  <ul class="mt-2">
  <li>
  Date Should be Between 1-31
  </li>
  <li>
  Month should be between 1-12
  </li></ul>
  Year should not be greater than {{ year }}
  </v-alert>
</v-row>
    <v-row align="center" justify="center">
      <v-card class="ma-5 px-5 pt-6 pb-5"  max-width="400" color="blue-grey-darken-4">
      <v-text-field
            v-model="getDate"
            label="Day"
            type="number"
            variant="outlined"
            clearable
            style="color: white;"
            min-width="300"
            hide-spin-buttons
            :rules="[rule1.required]"
          >
          </v-text-field>
          <v-text-field
            v-model="getMonth"
            label="Month"
            type="number"
            variant="outlined"
            clearable
            style="color: white;"
            min-width="300"
            hide-spin-buttons
            :rules="[rule1.required]"
          >
          </v-text-field>
          <v-text-field
            v-model="getYear"
            label="Year"
            type="number"
            variant="outlined"
            clearable
            style="color: white;"
            min-width="300"
            hide-spin-buttons
            :rules="[rule1.required]"
          >
          </v-text-field>
          <P align="center" class="mb-4" v-if="isTrue">
             <span style="color: #72c0ff;font-weight: 600;">{{ ageYear }}</span> years 
             <span style="color: #72c0ff;font-weight: 600;">{{ ageMonth }}</span> months 
             <span style="color: #72c0ff;font-weight: 600;">{{ ageDate }}</span> days
          </P>
          <v-btn
          v-if="!isTrue" 
          @click="ageCalc"
          color="indigo"
          class="w-100"
          style="text-transform: unset !important;"
          >Calculate Age</v-btn>
          <v-btn 
          @click="Reset"
          v-if="isTrue" 
          class="w-100"
          style="text-transform: unset !important;"
          >Reset</v-btn>
    </v-card>
    </v-row>

     
    </v-responsive>
  </v-container>
</template>

<script setup>
  import { ref } from 'vue'
  const d = new Date();
  var year = Number(d.getFullYear());
  var month = Number(d.getMonth()+1);
  var date = Number(d.getDate());
  var ageYear = ref('');
  var ageMonth = ref('');
  var ageDate = ref('');
  const getDate = ref('');
  const getMonth = ref('');
  const getYear = ref('');
  var isTrue = ref(false);
  var isAlert = ref(false);
  const rule1 = ref({
  required: value => !!value || 'Field is required', 
});

  const ageCalc = () =>{
    if(Number(getMonth.value) > 12 || Number(getDate.value) > 31 || Number(getYear.value) > year){
      isAlert.value = true;
      console.log(getYear.value)
      console.log(getMonth.value)
      console.log(getDate.value)
    }
    else if(Number(getYear.value) == year){
      if(Number(getMonth.value) > month || Number(getDate.value) > date){
        isAlert.value = true;
      }
      else{
        if(getMonth.value.length > 0 && getDate.value.length > 0 && getYear.value.length > 0){
      isTrue.value = true;
    }
    if(month>=Number(getMonth.value)){
      if(date>=Number(getDate.value)){
        ageYear.value = year-Number(getYear.value);
        ageMonth.value = month-Number(getMonth.value);
        ageDate.value = date-Number(getDate.value);
      }
      else{
        ageYear.value = year-Number(getYear.value);
        ageMonth.value = month-Number(getMonth.value)-1;
        ageDate.value = 30-(Number(getDate.value)-date);
      }
  }
  else{
    if(date>=Number(getDate.value)){
        ageYear.value = year-Number(getYear.value)-1;
        ageMonth.value = month+(12-Number(getMonth.value));
        ageDate.value = date-Number(getDate.value);
      }
      else{
        ageYear.value = year-Number(getYear.value)-1;
        ageMonth.value = month+(12-Number(getMonth.value));
        ageDate.value = 30-(Number(getDate.value)-date);
      }
        
  }
      }
      
    }
    else{
    if(getMonth.value.length > 0 && getDate.value.length > 0 && getYear.value.length > 0){
      isTrue.value = true;
    }
    if(month>=Number(getMonth.value)){
      if(date>=Number(getDate.value)){
        ageYear.value = year-Number(getYear.value);
        ageMonth.value = month-Number(getMonth.value);
        ageDate.value = date-Number(getDate.value);
      }
      else{
        ageYear.value = year-Number(getYear.value);
        ageMonth.value = month-Number(getMonth.value)-1;
        ageDate.value = 30-(Number(getDate.value)-date);
      }
  }
  else{
    if(date>=Number(getDate.value)){
        ageYear.value = year-Number(getYear.value)-1;
        ageMonth.value = month+(12-Number(getMonth.value));
        ageDate.value = date-Number(getDate.value);
      }
      else{
        ageYear.value = year-Number(getYear.value)-1;
        ageMonth.value = month+(12-Number(getMonth.value));
        ageDate.value = 30-(Number(getDate.value)-date);
      }
        
  }
    console.log(ageYear.value)
    console.log(ageMonth.value)
    console.log(ageDate.value)
  }

}
const Reset = () =>{
  getDate.value = '';
  getMonth.value = '';
  getYear.value = '';
  isTrue.value = false;
} 
</script>
