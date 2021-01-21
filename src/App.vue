<template>
  <div id="app" class="container-fluid">
    <div class="container-fluid">
        <div>
          <Topic v-if="state == 0" v-on:setTopic = "updateTopic($event)"/>
          <Entry :socket="socket" v-if="state == 1" :topic="topic" v-on:setCharacter = "updateCharacter($event)"/>
          <Chat v-if="state == 2" :character="selectedCharacter" :name="name" :socket="socket" :topic="topic"/>
        </div>
    </div>
  </div>
</template>

<script>
import Entry from './components/entry.vue'
import Chat from './components/chat.vue'
import Topic from './components/topicselection.vue'
import io from 'socket.io-client';
export default {
  data() {
    return {
      state:0,
      selectedCharacter:null,
      name:null,
      topic:null,
      socket : io('http://immense-citadel-10641.herokuapp.com',{
        "rejectUnauthorized" : false
      })
    }
  },
  name: 'app',
  components: {
    Entry,
    Chat,
    Topic
  },
  methods: {
    updateCharacter(params) {
      this.selectedCharacter = params.character;
      this.name = params.name;
      this.state = 2;
      console.log('this.selectedCharacter',this.selectedCharacter);
    },
    updateTopic(params) {
      this.topic = 'The one where ' + params;
      let topicObj = {
        isTopic:true, 
        topic:this.topic
      }
      this.state = 1;
      this.socket.emit('SEND_MESSAGE', topicObj);
    }
  },
  mounted() {
      this.socket.on('MESSAGE', (data) => {
          if(data.isTopic) {
              this.state = 1;
              this.topic = data.topic;
          } 
      });
  }
}

</script>

<style>

</style>