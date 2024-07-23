<template>
    <v-container class="fill-height">
    <v-responsive
      class="align-centerfill-height mx-auto"
      max-width="900"
    >
    <h1 align="center" class="mb-8">Chat Room</h1>
    <v-row align="center" justify="center">
        <v-card min-height="200"  class="pa-10" v-if="iscard">
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
              <v-text-field placeholder="Username" v-model="userNameEnter">

              </v-text-field>
            </v-row>
            <v-row>
              <v-text-field v-model="user2" placeholder="Code"></v-text-field>
            </v-row>
            <v-row align="center" justify="center">
              <v-btn @click="joinChat" style="text-transform: unset !important;" class="pa-2" color="light-blue-darken-3" width="100">
                    Join
              </v-btn> 
            </v-row>

            </div>
            <div v-if="isCode" class="my-5">
              <v-row>
              <v-text-field placeholder="Username" v-model="userNameCode">

              </v-text-field>
            </v-row>
            <v-row class="ma-5" align="center" justify="center" >
              <p align="center" class="my-2 mx-10"><span style="color: #90A4AE;">Code</span> : {{ code }}</p>
            <v-btn @click="startMessaging" color="blue-grey-darken-4" style="text-transform: unset !important;">
                Start Messaging
            </v-btn>
            </v-row>
            </div>

            
        </v-card>
    </v-row>
    <div v-if="isJoin" class="enter code">
      <v-row align="center" justify="center">
        <v-card width="500" class="px-5 py-3">
        <h4>Code : {{ user }}</h4>
      </v-card>
      </v-row>
        <ChatBox2  :user="user" ></ChatBox2>
        <v-row align="center" justify="center">
      <v-col cols="auto">
        <v-text-field v-model="message" placeholder="Message" max-width="500" min-width="400" @keyup.enter="sendMessage2" ></v-text-field>
      </v-col>
      <v-col cols="auto">
        <v-btn type="submit" color="primary" @click="sendMessage2" class="mb-5">Send</v-btn>
      </v-col>
    </v-row>
    </div>

    <div v-if="isStartMsg" class="mt-3">
      <v-row align="center" justify="center">
        <v-card width="500" class="px-5 py-3">
        <h4>Code : {{ code }}</h4>
      </v-card>
      </v-row>

    <ChatBox  :code="code" ></ChatBox>
    <v-row align="center" justify="center">
      <v-col cols="auto">
        <v-text-field v-model="message" placeholder="Message" max-width="500" min-width="400" @keyup.enter="sendMessage"></v-text-field>
      </v-col>
      <v-col cols="auto">
        <v-btn type="submit" color="primary" @click="sendMessage" class="mb-5">Send</v-btn>
      </v-col>
    </v-row>

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
const channel = new BroadcastChannel('chat-channel');

// localStorage.setItem('messages',JSON.stringify(messages.value));
const startMessaging = () => {
  console.log(userNameCode.value.length)
  if(userNameCode.value.length > 0){
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
    user.value = user2.value;
    let storedMessages = JSON.parse(localStorage.getItem('messages'));
    let tempIndex = storedMessages.findIndex(item => item.code == user.value);
    if (tempIndex !== -1 && userNameEnter.value.length > 0) {
      iscard.value = false;
      isJoin.value = true;
      
    } else {
    
      console.warn('Conversation code not found:', code.value);
    }
    

};

const sendMessage = () => {
  let date = new Date();
  let hour = date.getHours();
  let min = date.getMinutes();
  let time = hour+":"+min;


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
    
    // storedMessages.map(item => {
    //   if( item.code === code.value ){
    //     storedMessages.at(tempIndex).messages.push({ sender: userNameCode.value, content: message.value, msgTime: time });
    //   }
    //   else {
    //     console.warn('Code not found:', code.value);
    //   }
    // }
    
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
  
  
};


const sendMessage2 = () => {
  let date = new Date();
  let hour = date.getHours();
  let min = date.getMinutes();
  let time = hour+":"+min;
  console.log(user.value)

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
    
  //   storedMessages.map(item => {
  //     if( item.code == user.value ){
  //       storedMessages.at(tempIndex).messages.push({ sender: userNameEnter.value, content: message.value, msgTime: time });
  //     }
  //     else {
  //       console.warn('Code not found:', code.value);
  // }
  //   }
    
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