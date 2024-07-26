<script setup>
import { ref,onMounted } from 'vue';
import ChatBox from './ChatBox.vue';
const channel = new BroadcastChannel('chat-channel');

const messages = ref([]);

const message = ref('');
const code = ref('');
const user2 = ref('');
const user = ref('');
const isCode = ref(false);
const isStartMsg = ref(false);
const iscard = ref(true);
const isEnter = ref(false);
const userNameCode = ref('');
const userNameEnter = ref('');
const isJoin = ref(false);
const isRules = ref(false);
const isRequired = ref(false);
const isCodeTrue = ref(false);

const isCopy = ref(false);
const nameRules = ref([
  value => {
    if(value){
      return true
    }
    else{
      return 'Name is Required.'
    }
  }
]);
const codeRules = ref([
  value => {
    if(value){
      return true
    }
    else{
      return 'Code is Required.'
    }
  }
]);

// localStorage.setItem('messages',JSON.stringify(messages.value));

const startMessaging = () => {
  console.log(userNameCode.value.length)
  const storedMessages = localStorage.getItem('messages');
  
  if(userNameCode.value.length > 0 && !storedMessages){
    messages.value.push({ code: code.value, owner: userNameCode.value, messages: [] });
    console.log(message.value);
    localStorage.setItem('messages',JSON.stringify(messages.value));
    
    console.log("storeee",storedMessages)
    isStartMsg.value = true;
    iscard.value = false;
  }
  if(userNameCode.value.length > 0 && storedMessages){
    const data = JSON.parse(storedMessages);
    data.push({ code: code.value, owner: userNameCode.value, messages: [] });
    localStorage.setItem('messages',JSON.stringify(data));
    console.log("data",data);
    isStartMsg.value = true;
    iscard.value = false;
  }
};

const generateCode = () => {
  code.value = Math.random().toString(36).substring(2, 7).toUpperCase();
  isCode.value = true;
  isEnter.value = false;
  isCopy.value = false;

};

const enterCode = () => {
  isCode.value = false;
  isEnter.value = true;

};

const joinChat = () =>{
    user.value = user2.value.trim();
    let storedMessages = JSON.parse(localStorage.getItem('messages'));
    let tempIndex = storedMessages.findIndex(item => item.code == user.value);
    if(userNameEnter.value.length == 0 || user.value.length == 0){
      isRequired.value = true;
    }
    else{
      isRequired.value = false;
      if (tempIndex !== -1) {
      iscard.value = false;
      isJoin.value = true;
      isCodeTrue.value = false;
      } else {
        isCodeTrue.value = true;
      }
    }
};

const sendMessage = () => {
  let date = new Date();
  let hour = date.getHours();
  let min = date.getMinutes();
  let time = hour+":"+min;

  if(message.value.length == 0){
    isRules.value = true;
    return
  }
  else{
    isRules.value = false;
    let storedMessages = JSON.parse(localStorage.getItem('messages'));
    let tempIndex = storedMessages.findIndex(item => item.code == code.value);
    console.log("item code",tempIndex);

      if( tempIndex !== -1 ){
        storedMessages.at(tempIndex).messages.push({ sender: userNameCode.value, content: message.value, msgTime: time });
        localStorage.setItem('messages',JSON.stringify(storedMessages));
      }
      else {
        console.warn('Code not found:', code.value);
      }
    
    storedMessages = JSON.parse(localStorage.getItem('messages'));
    message.value = '';
 
    console.log("store",storedMessages)
    if (storedMessages) {
      messages.value = storedMessages;
      console.log("st0000",messages.value);
    };

    channel.postMessage(storedMessages);
  }
  channel.onmessage = (msg) =>{
    console.log("msg ==",msg)
    }
  
  
};


const sendMessage2 = () => {
  let date = new Date();
  let hour = date.getHours();
  let min = date.getMinutes();
  let time = hour+":"+min;
  console.log(user.value)
  if(message.value.length == 0){
    isRules.value = true;
    return
  }
  else{
    isRules.value = false;
  let storedMessages = JSON.parse(localStorage.getItem('messages'));
    let tempIndex = storedMessages.findIndex(item => item.code == user.value);
    console.log("item code",tempIndex);

      if( tempIndex !== -1 ){
        storedMessages.at(tempIndex).messages.push({ sender: userNameEnter.value, content: message.value, msgTime: time });
        localStorage.setItem('messages',JSON.stringify(storedMessages));
      }
      else {
        console.warn('Code not found:', code.value);
    }
  storedMessages = JSON.parse(localStorage.getItem('messages'));
  message.value = '';
  channel.onmessage = (msg) =>{
  console.log("msg ==",msg)
  }
  console.log("store",storedMessages)
  if (storedMessages) {
    messages.value = storedMessages;
    console.log("st0000",messages.value);
  };

  channel.postMessage(storedMessages);
}
};
const onCopySuccess = () =>{
  isCopy.value = true;
  setTimeout( () =>{
    isCopy.value = false;
  },3000
  );
};


// onMounted(() => {
//   const storedMessages = localStorage.getItem('messages');
//   console.log("store",storedMessages)
//   if (storedMessages) {
//     messages.value = JSON.parse(storedMessages);
//     channel.postMessage(storedMessages);
//     console.log("st0000",messages.value);
//     channel.onmessage = (msg) =>{
//     console.log(" mount msg ==",msg)
//     }
//   }
// });

// watch(messages, (messages) => {
//   const storedMessages = localStorage.getItem('messages');
//   console.log("store",storedMessages)
//   if (storedMessages) {
//     messages.value = JSON.parse(storedMessages);
//     console.log("st00",messages.value)
//   }
// },{ deep: true });

// watch(messages, (messages) => {
//   localStorage.setItem('messages', JSON.stringify(messages.value));
// },{ deep: true });
// localStorage.clear();
console.log(messages.value);
</script>


<template>
  <v-container class="fill-height">
  <v-responsive
    class="align-centerfill-height mx-auto"
    
  >
  <h1  class="mb-2 text-center">Chat Room</h1>
  <div class="d-flex justify-center">
      <v-card min-height="200"  class="pa-10 ma-5" v-if="iscard">
          <p  class="mb-8 text-center">Start Messaging</p>
          <div class="d-flex justify-center">
            <v-btn class="mr-5" color="cyan-darken-4" @click="generateCode" style="text-transform: unset !important;">
              Genrate Code
            </v-btn>
            <v-btn color="cyan-darken-4" style="text-transform: unset !important;" @click="enterCode">
              Enter Code
            </v-btn>
          </div>

          <br>

          <div v-if="isCode" class="mb-5 mt-7">
            <div class="d-flex justify-center">
            <v-text-field placeholder="Username" v-model="userNameCode" max-width="350" :rules="nameRules" autofocus>

            </v-text-field>
          </div>
          <div class="ma-5 d-flex justify-center align-center flex-wrap" >
            <p  class="ma-5 text-center"><span style="color: #90A4AE;">Code</span> : {{ code }}</p>
            <v-btn style="text-transform: unset !important;font-size: 12px;" class="mr-4" variant="outlined" v-clipboard:copy="code" v-clipboard:success="onCopySuccess">{{ isCopy ? 'Copied' : 'Copy'}}</v-btn>
            <v-btn @click="startMessaging" color="blue-grey-darken-4" style="text-transform: unset !important;" class="ma-4">
              Start Messaging
            </v-btn>
          </div>
          </div>


          <div class="py-5 d-flex flex-column" v-if="isEnter" >
            
              <v-text-field :placeholder="isRequired ? 'Username Required' : 'Username'" v-model="userNameEnter" :rules="nameRules"></v-text-field>
              <v-text-field v-model="user2" :placeholder="isRequired ? 'Code Required' : 'Code'" :rules="codeRules" ></v-text-field>
            
            <v-btn @click="joinChat" style="text-transform: unset !important;" class="pa-2 align-self-center mt-2 w-100" color="light-blue-darken-3" width="100">
                  Join
            </v-btn> 
            <p  class="mt-5 text-center" style="color: rgb(179, 0, 0);font-weight: 600;" v-if="isCodeTrue">Invalid Code</p>
          </div>
          
      </v-card>
    </div>


    <div v-if="isStartMsg" class="mt-3">

      <ChatBox  :code="code" :username="userNameCode"></ChatBox>
        <v-row class="mx-lg-10 mx-2">
          <v-spacer></v-spacer>
        <v-col  sm="10" cols="10"  lg="8">
          <v-text-field v-model="message" :placeholder="isRules ? 'Message cannot be empty' : 'Message'"  @keyup.enter="sendMessage" class="w-100"></v-text-field>
        </v-col>
        <v-col  class="d-flex align-center justify-center" sm="2" cols="2" lg="1" >
          <v-btn type="submit" color="white" @click="sendMessage" class="mb-5"  text="Send" height="40" style="text-transform: unset !important;"></v-btn>
        </v-col>
        <v-spacer></v-spacer>
        </v-row>
        <!-- <p align="center" class="mt-1" style="color: rgb(179, 0, 0);font-weight: 500;" v-if="isRules">Message cannot be empty</p> -->
    </div>

  <div v-if="isJoin" class="mt-3">

      <ChatBox  :code="user" :username="userNameEnter"></ChatBox>
      <v-row class="mx-lg-10 mx-2">
        <v-spacer></v-spacer>
  
    <v-col sm="10" cols="10"  lg="8">
      <v-text-field v-model="message" :placeholder="isRules ? 'Message cannot be empty' : 'Message'" class="w-100" @keyup.enter="sendMessage2"  ></v-text-field>
    </v-col>
    <v-col class="d-flex align-center justify-center" sm="2" cols="2" lg="1" >
      <v-btn type="submit" color="white" @click="sendMessage2" class="mb-5"  text="Send" height="40" style="text-transform: unset !important;"></v-btn>
    </v-col>
    <v-spacer></v-spacer>
  </v-row>
  </div>


  </v-responsive>
  </v-container>

</template>