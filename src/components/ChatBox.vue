<template>
  <v-row align="center" justify="center" class="ma-5">
    <v-card width="500" height="300" class="pa-10 overflow-auto">
        <span v-for="msg in messages" :key="msg.code">
            <span v-for="message in msg.messages" :key="msg.sender" v-if="msg.code == code">
        <v-list two-line>
      <v-list-item >
        
        <v-list-item-content >
          <v-list-item-subtitle style="font-size: 15px;" class="mb-2 text-wrap">{{ message.sender }}</v-list-item-subtitle>
          <v-list-item-title style="font-size: 18px;" class="mb-1 text-wrap">{{ message.content }}</v-list-item-title>
          <v-list-item-subtitle>{{ message.msgTime }}</v-list-item-subtitle>
        </v-list-item-content>
        
      </v-list-item>
    </v-list>
</span>
</span>
    </v-card>
  </v-row>

</template>

<script setup>
import { defineProps,ref } from 'vue';
const channel = new BroadcastChannel('chat-channel');
const messages = ref(JSON.parse(localStorage.getItem('messages')));
const props = defineProps(
    {
        code : String,
    }
)
channel.onmessage = (msg) =>{
  messages.value = msg.data;
  console.log('onmeassage',messages.value);
}

</script>