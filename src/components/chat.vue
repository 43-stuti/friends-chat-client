<template>
  <div class="container-fluid main">
        <div class="chatinfo">
            <div class="heading">
                <h2 class="headingText textmod"> {{topic}} </h2>
                <div class="separator"></div>
            </div>
            <div class="userInfo">
                <div class="character-image">
                    <img :src="characters[character].src">
                </div>
                <h4 class="mont info"> Your messages will be modified by {{characters[character].name}} </h4>
            </div>
            <div class="userContainer">
                <div class="users" v-for="(user,index) in onlineUsers" :key="index">
                    <div class="dot"></div>
                    <h6 class="username">{{user.name}}</h6>
                </div>
            </div>
        </div>
        <div class="chatscreen">
           <div>
                <div ref="messageView" class="chats">
                    <div class="messages" v-for="(msg, index) in messages" :key="index">
                        <div v-bind:style="chatBubbleStyle(index)">
                            <chatbubble :message="msg"></chatbubble>
                        </div>
                    </div>
                </div>
            </div>
            <div class="separatorChat"></div>
            <div class="message">
                <form @submit.prevent="sendMessage">
                    <div class="gorm-group pb-3">
                        <div class="displayprompt"> {{displayText}} </div>
                        <input type="text" autocomplete="off" v-model="message" class="form-control" id="custom">
                    </div>
                </form>
            </div>
        </div>
  </div>
</template>

<script>
import chatbubble from './chatbubble.vue'
export default {
    data() {
        return {
            user: '',
            message: '',
            messages: [],
            characterMapping: {
                0:'Chandler',
                1:'Joey',
                2:'Monica',
                3:'Phoebe',
                4:'Rachel',
                5:'Ross'
            },
            characters : [{
                    src:'/monica.png',
                    name:'Chandler'
                }, {
                    src:'/joey.png',
                    name:'Joey'
                }, {
                    src:'/monica.png',
                    name:'Monica'
                }, {
                    src:'/phoebe.png',
                    name:'Phoebe'
                }, {
                    src:'/rachel.png',
                    name:'Rachel'
                }, {
                    src:'/ross.png',
                    name:'Ross'
                }],
            onlineUsers: [],
            labelState: 0
        }
    },
    components: {
        chatbubble
    },
    props:['character','name','socket','topic'],
    computed: {
        characterName() {
            return this.characterMapping[this.character]
        },
        displayText() {
            let labelText = {
                0:'All you text will be ' + this.characterMapping[this.character] + '-fied',
                1:'Your text is now getting ' + this.characterMapping[this.character] + '-fied',
                2:'Your text has been successfully ' + this.characterMapping[this.character] + '-fied'
            }
            return labelText[this.labelState]
        }
    },
    watch: {
        messages: function() {
            this.$refs.messageView.scrollTop = this.$refs.messageView.scrollHeight - this.$refs.messageView.clientHeight;
        },
        onlineUsers: function() {
            console.log(this.onlineUsers.length)
        }
    },
    methods: {
        sendMessage(e) {
            e.preventDefault();
            let msgObj = {
                character: this.characterName,
                user: this.name,
                message: this.message
            }
            this.socket.emit('SEND_MESSAGE', msgObj);
            //this.messages = [...this.messages, msgObj];
            this.message = '';
            this.labelState = 1;
        },
         chatBubbleStyle(index) {
            let margin = '0px';
            if(this.messages[index-1] && this.messages[index-1].user == this.messages[index].user) {
                margin = '1px'
            } else {
                margin = '10px'
            }
            if(this.messages[index].user == this.characterName)   {
                return {
                    'display': 'block',
                    'position': 'relative',
                    'float':'right',
                    'clear': 'both',
                    'margin-top':margin
                };
            } else {
                return {
                    'display': 'block',
                    'position': 'relative',
                    'float':'left',
                    'clear': 'both',
                    'margin-top':margin
                };
            }
        }
    },
    mounted() {
        let introObj = {
            intro:true, 
            user:this.characterMapping[this.character],
            name:this.name,
            fromChat:true
        }
        this.socket.emit('SEND_MESSAGE', introObj);
        this.socket.emit('SEND_MESSAGE', {
            fetchUsers:true,
            fromChat:true
        });
        this.socket.emit('SEND_MESSAGE', {
            fetchMessages:true,
            fromChat:true
        });
        this.socket.on('MESSAGE', (data) => {
            if(data.intro) {
                for(let i=0;i<data.users.length;i++) {
                    let exists = false;
                    for(let j=0;j<this.onlineUsers.length;j++) {
                        if(data.users[i].user == this.onlineUsers[j].user) {
                            exists = true;
                        }
                    }
                    if(!exists) {
                        this.onlineUsers.push(data.users[i])
                    }
                }
                //this.onlineUsers = this.onlineUsers.concat(data.users);
            } else {
                if(data.reset) {
                    this.onlineUsers = data.users;
                } else {
                    console.log('messages',data)
                    if(data.initalLoad && data.messages && data.messages.length) {
                        this.messages = data.messages
                    } else {
                        if(data.message) {
                            this.messages = [...this.messages, data];
                        }
                        if(data.character == this.characterName) {
                            data.self = true;
                            this.labelState = 2;
                            setTimeout(() =>{
                                this.labelState = 0;
                            },2000)
                        }
                    }
                }
            }
            // you can also do this.messages.push(data)
        });
    }
}
</script>

<style scoped>
@font-face {
        font-family: 'friendsfont';
        src: url("./../assets/fonts/gabrwffr.woff");
        font-weight: normal;
        font-style: normal;
    }
    @font-face {
        font-family: 'montserrat';
        src: url('https://fonts.googleapis.com/css2?family=Montserrat&display=swap');
        font-weight: normal;
        font-style: normal;
    }
    .mont {
        font-family: "montserrat";
        font-weight: 800;
        font-size: 16px;
        color: rgb(34, 34, 34);
    }
    .montSm {
        font-family: "montserrat";
        font-weight: 500;
        font-size: 12px;
    }
    .textmod {
        font-family: "friendsfont";
        font-size: 25px;
        font-weight: 20;
    }
    .main {
        height: 100vh;
    }
    .chatinfo {
        display: inline-block;
        width: 40%;
        height: 100vh;
        position: relative;
        background-color: rgb(255, 255, 255);
    }
    .chatscreen {
        display: inline-block;
        width: 60%;
        height: 100vh;
        background-color: #1c1c1b;
        vertical-align: top;
        color: rgb(255, 253, 244);
        overflow: hidden;
    }
    .heading {
        height: 10vh;
    }
    .headingText {
        text-align: center;
         
    }
    .separatorChat {
        margin-right: 0px;
        margin-left: 20px;
        height: 0.9px;
        width: 95%;
        background-color:#4b4b4b;
        margin-bottom: 40px;
    }
    .chats {
        height: 80vh;
        overflow: auto;
        -webkit-overflow-scrolling: touch;
        -ms-overflow-style: none;
    }
    .chats::-webkit-scrollbar {
        display: none;
    }
    .message {
        padding-left: 30px;
        padding-right: 30px;
        height: 20vh;
        vertical-align: end;
    }
    .displayprompt {
        margin-bottom: 10px;
        font-size: 14px;
        font-weight: 100;
    }
    .heading {
        margin-top: 30vh;
        margin-right: 40px;
    }
    .userContainer {
        margin-top: 30px;
        margin-left: 13vw;
    }
    .dot {
        height: 10px;
        width: 10px;
        background-color: rgb(65, 65, 65);
        border-radius: 50%;
        display: inline-block;
        margin-right: 20px;
    }
    .username {
        display: inline-block;
    }
    #custom {
        border: none;
        background: transparent;
        border: 1px solid;
        border-radius: '100px';
        border-color:#858585
    }
    .separator {
        background-color: rgb(15, 80, 201);
        height: 5px;
        display: block;
        margin-bottom: 40px;
    }
    .character-image {
        display: inline-block;
        width: 50px;
        height: 50px;
    }
    img {
        max-width: 100%;
        max-height: 100%;
    }
    .userInfo {
        margin-top: 40px;
        background-color: rgb(203, 220, 253);
        margin-right: 40px;
    }
    .info {
        display: inline-block;
        margin-left: 20px;
    }
</style>