<script setup>
import { defineProps,ref } from 'vue';
import DialogBox from './DialogBox.vue';
const channel = new BroadcastChannel('chat-channel');
channel.onmessage = async (msg) =>{
  messages.value =  msg.data;
  console.log('onmeassage',messages.value);
}
const messages = ref(JSON.parse(localStorage.getItem('messages')));
const isCopy = ref(false);
const userList = ref();
const isOpen = ref(false);


const props = defineProps(
    {
        code : String,
        username : String,
    }
);
const onCopySuccess = () =>{
  isCopy.value = true;
  setTimeout( () =>{
    isCopy.value = false;
  },3000
  );
};
// onMounted(
//   channel.onmessage = (msg) =>{
//   messages.value = msg.data;
//   console.log('onmeassage',messages.value);
// }
// );

const getUsers = () =>{
 isOpen.value = true;
}
const deleteMsg = (i,index) =>{
  var storedMessages = JSON.parse(localStorage.getItem('messages'));
  storedMessages.at(i).messages.splice(index,1);
  localStorage.setItem('messages',JSON.stringify(storedMessages));
  channel.postMessage(storedMessages);
  messages.value = JSON.parse(localStorage.getItem('messages'));

}
</script>
 
<template>
  <span v-for="msg in messages" :key="msg.code" >

<div class="d-flex justify-center" v-if="msg.code == code">
  <v-card max-width="800" class="px-6 py-3 w-100">
  <div class="float-left d-flex flex-sm-row align-center flex-column">
    <h4 class="mt-1">Code : {{ code }}</h4>
    <v-btn style="text-transform: unset !important;font-size: 12px;" class="mx-5 mt-1" variant="outlined" v-clipboard:copy="code" v-clipboard:success="onCopySuccess" height="25">{{ isCopy ? 'Copied' : 'Copy'}}</v-btn>    
  </div>
  <div class="float-right d-flex flex-sm-row align-center flex-column align-center">
    <h4 class="mt-1">Owner : {{ msg.owner }}</h4>
    
    <v-btn 
        v-if="msg.owner == username" 
        height="30"
        style="text-transform: unset !important;font-size: 12px;" 
        class="mx-5 mt-1" 
        variant="outlined"
        @click="getUsers"
        
        text="Users List">

    </v-btn>
    
    <DialogBox :username="username" :code="code" v-if="isOpen" @close="isOpen = false"></DialogBox>
    
  
  </div>


    
  </v-card>
</div>

</span>
  <div class="ma-5 d-flex justify-center">

    <v-card width="800" height="400" class="py-10 pl-2 overflow-auto d-flex flex-column-reverse">
      <span v-for="msg in messages" :key="msg.code" >
 
            <span v-for="(message, index) in msg.messages" :key="index" v-if="msg.code == code" >
        <v-list two-line id="list">
    
        

          <v-card  color="teal-lighten-5" class="px-1 py-1 mx-2" :style="[ username == message.sender ? {'float': 'right','width' : 'fit-content'} : {'float': 'left','width' : 'fit-content'}]" >
            <v-list-item>
              <div class="d-flex flex-column">
              <div class="d-flex flex-row mt-1" :class="[ username == message.sender ? 'justify-end' : 'justify-start']">
                <v-list-item-subtitle style="font-size: 14px;" class="mb-2 text-wrap">{{ message.sender }}</v-list-item-subtitle>
                <v-list-item-subtitle class="ml-2" style="font-size: 12px;" >{{ message.msgTime }}</v-list-item-subtitle>
              </div>
              <div class="d-flex flex-row">
                <v-list-item-title style="font-size: 16px;" class="mb-1 text-wrap">{{ message.content }}</v-list-item-title>
              </div>
              </div>

          </v-list-item>
          </v-card>
   
        

    </v-list>
</span>
</span>
    </v-card>

  </div>

</template>