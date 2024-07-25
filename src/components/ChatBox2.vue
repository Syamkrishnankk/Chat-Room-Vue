<template>
  <v-row align="center" justify="center" class="ma-5">
    <v-card width="800" height="400" class="py-10 pl-2 overflow-auto messagelist" >
        <span v-for="msg in messages" :key="msg.code" >
            <span v-for="message in msg.messages" :key="msg.sender" v-if="msg.code == user" >
        <v-list two-line  >


          <v-card color="teal-lighten-5" class="px-1 py-1 mx-2" :style="[ username === message.sender ? {'float': 'right','width' : 'fit-content'} : {'float': 'left','width' : 'fit-content'}]">
            <v-list-item >
            <v-row class="ma-1">
    
    <v-list-item-subtitle style="font-size: 14px;" class="mb-2 text-wrap">{{ message.sender }}</v-list-item-subtitle>


    <v-list-item-subtitle class="ml-2" style="font-size: 12px;" >{{ message.msgTime }}</v-list-item-subtitle>

</v-row>
<v-list-item-title style="font-size: 16px;" class="mb-1 text-wrap">{{ message.content }}</v-list-item-title>
</v-list-item>         
</v-card>
        

    </v-list>
</span>
</span>
    </v-card>
  </v-row>

</template>

<style scoped>
.messagelist{
  display: flex;
  flex-direction: column-reverse;
  overflow-y: scroll;
}

</style>

<script setup>
import { onMounted } from 'vue';
import { defineProps,ref,watch } from 'vue';
const messageList = ref(null);
const channel = new BroadcastChannel('chat-channel');
let messages = ref(JSON.parse(localStorage.getItem('messages')));
const props = defineProps(
    {
        user : String,
        username : String,
    }
)

channel.onmessage = (msg) =>{
  messages.value = msg.data;
  console.log('onmsg',messages.value);
}

</script>
