<template>
    <v-container class="fill-height">
    <v-responsive
      class="align-centerfill-height mx-auto"
      
    >
    <h1 align="center" class="mb-8">Chat Room</h1>
    <v-row align="center" justify="center">
        <v-card min-height="200"  class="pa-10 ma-5" v-if="iscard">
            <p align="center" class="mb-8">Start Messaging</p>
            <v-row align="center" justify="center">
              <v-btn class="mr-5" color="cyan-darken-4" @click="generateCode" style="text-transform: unset !important;">
                Genrate Code
              </v-btn>
              <v-btn color="cyan-darken-4" style="text-transform: unset !important;" @click="enterCode">
                Enter Code
              </v-btn>
            </v-row>

            <br>
            <div class="py-5" v-if="isEnter" >
              <v-row>
              <v-text-field :placeholder="isRequired ? 'Username Required' : 'Username'" v-model="userNameEnter" :rules="nameRules" >

              </v-text-field>
            </v-row>
            <v-row>
              <v-text-field v-model="user2" :placeholder="isRequired ? 'Code Required' : 'Code'" :rules="codeRules" ></v-text-field>
            </v-row>
            <v-row align="center" justify="center">
              <v-btn @click="joinChat" style="text-transform: unset !important;" class="pa-2" color="light-blue-darken-3" width="100">
                    Join
              </v-btn> 
            </v-row>
            <p align="center" class="mt-5" style="color: rgb(179, 0, 0);font-weight: 600;" v-if="isCodeTrue">Invalid Code</p>
            </div>
            <div v-if="isCode" class="mb-5 mt-7">
              <v-row align="center" justify="center">
              <v-text-field placeholder="Username" v-model="userNameCode" max-width="350" :rules="nameRules" autofocus>

              </v-text-field>
            </v-row>
            <v-row class="ma-5" align="center" justify="center" >
              <p align="center" class="my-2 ml-5 mr-2"><span style="color: #90A4AE;">Code</span> : {{ code }}</p>
              <v-btn style="text-transform: unset !important;font-size: 12px;" class="mr-5" variant="outlined" v-clipboard:copy="code" v-clipboard:success="onCopySuccess">{{ isCopy ? 'Copied' : 'Copy'}}</v-btn>
            <v-btn @click="startMessaging" color="blue-grey-darken-4" style="text-transform: unset !important;">
                Start Messaging
            </v-btn>
            </v-row>
            </div>

            
        </v-card>
    </v-row>
    <div v-if="isJoin" class="enter code mt-3">
      <v-row align="center" justify="center">
        <v-card width="800" class="px-8 py-5">
          <v-row >
            <h4>Code : {{ user }}</h4>
            <v-btn style="text-transform: unset !important;font-size: 12px;" class="mx-5" variant="outlined" v-clipboard:copy="user" v-clipboard:success="onCopySuccess" height="25">{{ isCopy ? 'Copied' : 'Copy'}}</v-btn>
          </v-row>
        </v-card>
      </v-row>
        <ChatBox2  :user="user" :username="userNameEnter"></ChatBox2>
        <v-row align="center" justify="center">
      <v-col cols="auto">
        <v-text-field v-model="message" :placeholder="isRules ? 'Message cannot be empty' : 'Message'" max-width="800" min-width="700" @keyup.enter="sendMessage2"  ></v-text-field>
      </v-col>
      <v-col cols="auto">
        <v-btn type="submit" color="white" @click="sendMessage2" class="mb-5"  text="Send" height="40" style="text-transform: unset !important;"></v-btn>
      </v-col>
    </v-row>
    </div>

    <div v-if="isStartMsg" class="mt-3">
      <v-row align="center" justify="center">
        <v-card width="800" class="px-8 py-5">
          <v-row >
            <h4>Code : {{ code }}</h4>
            <v-btn style="text-transform: unset !important;font-size: 12px;" class="mx-5" variant="outlined" v-clipboard:copy="code" v-clipboard:success="onCopySuccess" height="25">{{ isCopy ? 'Copied' : 'Copy'}}</v-btn>
          </v-row>
        </v-card>
      </v-row>

    <ChatBox  :code="code" :username="userNameCode"></ChatBox>
    <v-row align="center" justify="center">
      <v-col cols="auto">
        <v-text-field v-model="message" :placeholder="isRules ? 'Message cannot be empty' : 'Message'" max-width="800" min-width="700" @keyup.enter="sendMessage" ></v-text-field>
      </v-col>
      <v-col cols="auto">
        <v-btn type="submit" color="white" @click="sendMessage" class="mb-5"  text="Send" height="40" style="text-transform: unset !important;"></v-btn>
      </v-col>
    </v-row>
    <!-- <p align="center" class="mt-1" style="color: rgb(179, 0, 0);font-weight: 500;" v-if="isRules">Message cannot be empty</p> -->
    </div>
    </v-responsive>
    </v-container>

</template>

<script setup>
import { ref,onMounted,watch } from 'vue';
import ChatBox from './ChatBox.vue';
import ChatBox2 from './ChatBox2.vue';

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
const channel = new BroadcastChannel('chat-channel');
const isCopy = ref(false);
const messageRules = ref([
  value => {
    if(value){
      return true
    }
    else{
      return 'Message cannot be empty'
    }
  }
]);
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

const onCopySuccess = () =>{
  isCopy.value = true;
};

// localStorage.setItem('messages',JSON.stringify(messages.value));
const startMessaging = () => {
  console.log(userNameCode.value.length)
  if(userNameCode.value.length > 0){
    isCopy.value = false;
    messages.value.push({ code: code.value, messages: [] });
    localStorage.setItem('messages',JSON.stringify(messages.value));
    const storedMessages = localStorage.getItem('messages');
    console.log("storeee",storedMessages)
    isStartMsg.value = true;
    iscard.value = false;
  }
};

const generateCode = () => {
  code.value = Math.random().toString(36).substring(2, 7).toUpperCase();
  isCode.value = true;
  isEnter.value = false;

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



onMounted(() => {
  const storedMessages = localStorage.getItem('messages');
  console.log("store",storedMessages)
  if (storedMessages) {
    messages.value = JSON.parse(storedMessages);
    channel.postMessage(storedMessages);
    console.log("st0000",messages.value);
    channel.onmessage = (msg) =>{
    console.log(" mount msg ==",msg)
    }
  }
});

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
