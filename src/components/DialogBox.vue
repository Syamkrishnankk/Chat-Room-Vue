<script setup>
import { defineProps,ref } from 'vue';
const channel = new BroadcastChannel('chat-channel');
channel.onmessage = async (msg) =>{
  messages.value =  msg.data;
  console.log('onmeassage',messages.value);
}
const messages = ref(JSON.parse(localStorage.getItem('messages')));

const userList = ref();
const isOpen = true;

const props = defineProps(
    {
        code : String,
        username : String,
        open : Boolean,
    }
);

// onMounted(
//   channel.onmessage = (msg) =>{
//   messages.value = msg.data;
//   console.log('onmeassage',messages.value);
// }
// );

var storeMessages = JSON.parse(localStorage.getItem('messages'));
console.log(storeMessages)
console.log(storeMessages.filter(code => code.code == props.code).map(sender => sender.messages))
console.log(new Set((storeMessages.filter(code => code.code == props.code).map(sender => sender.messages)).flatMap(innerArray => innerArray.map(obj => obj.sender))))
userList.value = [...new Set((storeMessages.filter(code => code.code == props.code).map(sender => sender.messages)).flatMap(innerArray => innerArray.map(obj => obj.sender)))];
console.log("userlist : ",userList.value)

const deleteMsg = (i,index) =>{
  var storedMessages = JSON.parse(localStorage.getItem('messages'));
  storedMessages.at(i).messages.splice(index,1);
  localStorage.setItem('messages',JSON.stringify(storedMessages));
  channel.postMessage(storedMessages);
  messages.value = JSON.parse(localStorage.getItem('messages'));

}
</script>


<template>
    <v-dialog v-model="isOpen" width="500">
        <v-card class="px-5">
      <h2  class="mb-2 mt-4 text-center">Users List</h2>
      <v-list >
      <v-list-group class="w-100 px-3 mb-3"  v-for="sender in userList"   :value="sender" style="background-color: rgb(50, 50, 50);">
        <template v-slot:activator="{ props }">
      <v-list-item v-bind="props" :title="sender"></v-list-item>
    </template>
<div class="overflow-auto" style="max-height: 300px;">
  
  <span v-for="(msg,i) in messages" :key="msg.code" >
      <span v-for="(message,index) in msg.messages" :key="msg.sender" v-if="msg.code == code" >
  <v-list two-line id="list" v-if="message.sender == sender">
    <v-card color="teal-lighten-5" class="px-1 py-1 mx-2"  >
      <v-list-item >
      <v-row class="ma-1">
          <v-list-item-subtitle style="font-size: 14px;" class="mb-2 text-wrap">{{ message.sender }}</v-list-item-subtitle>
          <v-list-item-subtitle class="ml-2" style="font-size: 12px;" >{{ message.msgTime }}</v-list-item-subtitle>
      </v-row>
      <v-row>
        <v-col cols="9">
          <v-list-item-title style="font-size: 16px;" class="mb-1 text-wrap">{{ message.content }}</v-list-item-title>
        </v-col>
        <v-col cols="3">
          <v-btn style="text-transform: unset !important;font-size: 14px;" @click="deleteMsg(i,index)">Delete</v-btn>
        </v-col>
      </v-row> 
      </v-list-item>
    </v-card>
  </v-list>
  </span>
  </span>
</div>

</v-list-group>
</v-list>
      <v-card-actions>
        <v-spacer></v-spacer>

        <v-btn text="Close" @click="$emit('close')"></v-btn>
      </v-card-actions>
    </v-card>
    </v-dialog>

  </template>