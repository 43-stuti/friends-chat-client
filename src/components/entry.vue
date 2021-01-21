<template>
    <div class="container-fluid full econtainer">
        <div class="marginSet">
        </div>
        <div class="headContainer">
            <h2 class="headingText textmod"> {{topic}} </h2>
            <div class="separator"></div>
        </div>
        <div class="mainContainer">
            <div class="headContainer">
                <h4 class="mont"> Who would you like your words to be twisted by? </h4>
            </div>
            <div class="charContainer">
                <div class="characters" v-for="(char,index) in characters" :key="index">
                    <div class="character-card"  @mouseover="transformButtom(1,index)" @mouseout="transformButtom(2,index)" @click="selectcharacter(index)" :class="{active: activeItem === index}">
                        <div class="selector">
                        </div>
                        <div>
                            <div class="alert" v-if="char.taken">
                                Unavailable
                            </div>
                            <div class="character-image">
                                <img :src="char.src">
                            </div>
                            <div class="character-name">
                                <div class="montSm">
                                    {{char.name}}
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="headContainer">
                <h4 class="set mont"> What are you called by your friends? </h4>
                <input type="text" autocomplete="off" v-model="name" class="form-control set1" id="custom">
            </div>
            <div class="buttonContainer">
                <div class="button" v-bind:style="buttonClass" @click="nextScreen()">
                    <p> Get started </p>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    export default {
        data() {
            return {
                characters : [{
                    src:'/chandler.png',
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
                activeItem: null,
                pickedCharacter: null,
                buttonState:0,
                name:'',
                onlineUsers:[]
            }
        },
        props:['socket','topic'],
        computed: {
            buttonClass() {
                console.log('buttonstate',this.buttonState)
                if(this.name.length != 0 && this.pickedCharacter) {
                    return {
                        'opacity':'1'
                    }
                } else {
                    return {
                        'opacity':'0.1'
                    }
                }
            }
        },
        methods: {
            selectcharacter(index) {
                console.log('index',index,this.characters[index]);
                if(!this.characters[index].taken) {
                    this.pickedCharacter = index;
                    this.activeItem = index;
                }
            },
            transformButtom(event,index) {
                if(event == 1 && !this.characters[index].taken && this.pickedCharacter == null) {
                    this.activeItem = index;
                } 
                if(event == 2 && this.pickedCharacter == null) {
                    this.activeItem = null;
                }
            },
            nextScreen() {
                if(this.name.length != 0 && this.pickedCharacter != null) {
                    let data = {
                        name:this.name,
                        character:this.pickedCharacter
                    }
                    this.$emit('setCharacter',data)
                }
            }
        },
        mounted() {
            this.socket.emit('SEND_MESSAGE', {
                fetchUsers:true
            });
            this.socket.on('MESSAGE', (data) => {
            if(!data.fromChat && (data.intro || data.reset)) {
                for(let i=0;i<data.users.length;i++) {
                    this.onlineUsers.push(data.users[i].user);
                   
                }
                for(let i=0;i<this.onlineUsers.length;i++) {
                    for(let j=0;j<this.characters.length;j++) {
                        if(this.characters[j].name.toLowerCase() == this.onlineUsers[i].toLowerCase()) {
                            this.characters[j].taken = true;
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
        font-size: 20px;
        color: rgb(124, 124, 124);
    }
    .montSm {
        font-family: "montserrat";
        font-weight: 500;
        font-size: 12px;
    }
    .mainContainer {
        margin-top: 50px;
    }
    .textmod {
        font-family: "friendsfont";
        font-size: 25px;
        font-weight: 20;
        color: rgb(255, 255, 255);
    }
    .headingText {
        text-align: center;
         
    }
    .character-card {
        opacity: 0.3;
    }
    .set {
        margin-top: 60px;
    }
    .set1 {
        margin-top: 30px;
    }
    img {
        max-width: 100%;
        max-height: 100%;
    }
    .econtainer {
        height: 100vh;
        background-color:#181817;
    }
    .characters {
        display: inline-block;
        width: 9vw;
        height: 14vh;
        margin-top: 30px;
        margin-left: 10px;
        color: rgb(255, 255, 255);
    }
    .character-name {
        margin-top: 10px;
    }
    .headContainer {
        display: table;
        margin: 0 auto;
        color: rgb(255, 255, 255);
    }
    .charContainer {
       display: table;
       margin: 0 auto;
    }
    .active {
        opacity: 1;
    }
    .button {
        border: 1px solid;
        border-color: #f0e911;
        padding: 3px;
        padding-left: 20px;
        padding-right: 20px;
        display: inline-block;
        display: table;
        margin: 0 auto;
        color: cornsilk;
        opacity: 0.5;
    }
    .buttonContainer {
        margin-top: 50px;
    }
    .marginSet {
        height: 5vh;
    }
    .separator {
        background-color: rgb(15, 80, 201);
        height: 5px;
        display: block;
        margin-bottom: 40px;
    }
    .alert {
        font-family: "montserrat";
        font-weight: 500;
        font-size: 12px;
        color: rgb(255, 255, 255);
        margin-left: -20px;
        margin-bottom: -10px;
    }
</style>