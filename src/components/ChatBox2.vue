<template>
<span v-for="msg in messages" :key="msg.code" >

<v-row align="center" justify="center" v-if="msg.code == user">
  <v-card width="800" class="px-8 py-5">
    <v-row >
      <h4 class="mt-1">Code : {{ user }}</h4>
        <v-btn style="text-transform: unset !important;font-size: 12px;" class="mx-5 mt-1" variant="outlined" v-clipboard:copy="user" v-clipboard:success="onCopySuccess" height="25">{{ isCopy ? 'Copied' : 'Copy'}}</v-btn>
        <h4 class="mt-1">Owner : {{ msg.owner }}</h4>
        <v-dialog max-width="500">
        <template v-slot:activator="{ props: activatorProps }">
          <v-btn v-if="msg.owner == username" 
        height="30"
        style="text-transform: unset !important;font-size: 12px;" 
        class="mx-5 mt-1" 
        variant="outlined"
        @click="getUsers"
        v-bind="activatorProps"
        text="User's List">

      </v-btn>
        </template>

        <template v-slot:default="{ isActive }">
          <v-card class="px-5">
            <h2 align="center" class="mb-2 mt-4">User's List</h2>
            <v-list >
            <v-list-group class="w-100 px-3 mb-3"  v-for="sender in userList"   :value="sender" style="background-color: rgb(50, 50, 50);">
              <template v-slot:activator="{ props }">
            <v-list-item v-bind="props" :title="sender"></v-list-item>
          </template>
      <div class="overflow-auto" style="max-height: 300px;">
        
        <span v-for="(msg,i) in messages" :key="msg.code" >
            <span v-for="(message,index) in msg.messages" :key="msg.sender" v-if="msg.code == user" >
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

              <v-btn text="Close" @click="isActive.value = false"></v-btn>
            </v-card-actions>
          </v-card>
        </template>
      </v-dialog>

    </v-row>
  </v-card>
</v-row>

</span>
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
const channel = new BroadcastChannel('chat-channel');
let messages = ref(JSON.parse(localStorage.getItem('messages')));
const isCopy = ref(false);
const userList = ref();
const props = defineProps(
    {
        user : String,
        username : String,
    }
);
const onCopySuccess = () =>{
  isCopy.value = true;
};

channel.onmessage = (msg) =>{
  messages.value = msg.data;
  console.log('onmsg',messages.value);
}
const getUsers = () =>{
  var storeMessages = JSON.parse(localStorage.getItem('messages'));
  console.log(storeMessages)
  console.log(storeMessages.filter(code => code.code == props.user).map(sender => sender.messages))
  console.log(new Set((storeMessages.filter(code => code.code == props.user).map(sender => sender.messages)).flatMap(innerArray => innerArray.map(obj => obj.sender))))
  userList.value = [...new Set((storeMessages.filter(code => code.code == props.user).map(sender => sender.messages)).flatMap(innerArray => innerArray.map(obj => obj.sender)))];
  console.log("userlist : ",userList.value)
}
const deleteMsg = (i,index) =>{
  var storedMessages = JSON.parse(localStorage.getItem('messages'));
  storedMessages.at(i).messages.splice(index,1)
  localStorage.setItem('messages',JSON.stringify(storedMessages));
  channel.postMessage(storedMessages);
  messages.value = JSON.parse(localStorage.getItem('messages'));

}
</script>
