# chat
### Overview

The idea for this project was to create a chat application where one let go off agency. What you said wouldn't necessarily be under your control. 
Have your words twisted and take the group conversation in a completely unanticipated direction.

I planned to have more "friends" show style conversation. I scraped the friends transcripts from [here](https://fangj.github.io/friends/) using cheerio. 
And trained a new model on RunwayML on those transcripts. 

<img src="https://user-images.githubusercontent.com/12654691/105268597-89186f00-5b60-11eb-8a62-880db25d3096.png" width="600px" height="300px">

Try out the chat [here](https://goofy-panini-b0b245.netlify.app)
- Set a topic for the chat and invite your friends by sharing the link

##### It works something like this. 
      Eg: I gave it a prompt of Joey saying "I am not feeling well today."
<img src="https://user-images.githubusercontent.com/12654691/105054311-d94be000-5a3f-11eb-934f-970524f90dea.png"></img>
Once I hosted the model, I could communicate with it via it's REST API. 

### How it works 
- The person initiating the chat sets the topic for the chat eg: "The one where __________"
- The host then invites other friends using the link. 
- They all land on a screen where they are asked to choose the character which would twist their words and also enter their own name. 
- Let the twisted chat begin. 

Behind the scenes 
- If a user called "Mary" has picked "Monica" all her texts will be redone by monica. 
- Before sending the prompt to runway the server replaces all the personal names with the respective character names.
Eg: If another user Tom picked Joey
"Mary: I hate Tom and Tom's cat" becomes "Monica: I hate Joey and Joey's cat" where Tom has picked Joey. 
- The response from runway is modified in the opposite way. ie replacing character names with the respective users before it gets sents to all the clients. 
eg:  "Monica: I hate Joey and Joey's cat" becomes "Mary: I hate Tom and Tom's cat" becomes
    
### Making the chat application 
    - The server for the app is made using NodeJS and SocketIO
    - The client is made on VueJS and the routing is handled by vue-router
#### The chatrooms 
The server maintains a reference to all chat rooms. 
A Chatroom has the following structure 
<pre>
|-- Topic
|-- Messages
|     |- [{
|           user:'Tom',
|           character:'Joey',
|           message:'Hi'
|         }]
|- onlineUsers
|     |- [{
|           socket:
|           user:'Tom',
|           character:'Joey'
|         }]
</pre>
It communicates with the sockets and runway


## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
