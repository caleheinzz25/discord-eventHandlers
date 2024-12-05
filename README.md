# discord-eventHandlers Documentation

## Installation

To install the package, use the following command:

```bash
npm install djs-eventhandlers
```

## Setup guide

```js
import { EventHandlers } from 'discord-eventmanager';
import { Client } from 'discord.js';

// Initialize your Discord client
const client = new Client({ intents: [] }); // Configure intents as needed

// Create a new instance of EventHandlers
new EventHandlers({
    client,
    commandsPath: "src/commands", // Path to your commands folder or file
    eventsPath: "src/events", // Path to your events folder or file
    db: {  
        dbPath: "src/db", // Path to the database folder
        database: [
            "mongoose" // Name of the database folder within the db folder
        ] 
    },
    devServer: "943929342490482" // Optional: Specify a development server for exclusive command updates
});

```
## example structure folders / files

folder / file structure

```sh
├── src
│   ├── commands
│   │   ├── moderator
│   │   │   ├── ping.js // you can nested a much as u want  
│   │   ├── admin
│   │   ├── misc
│   │   ├── daily
│   ├── events
│   │   ├── messageCreate
│   │   │   ├── 01Index.js  // This file will execute before index.js
│   │   │   ├── index.js    // You can name this file as needed
│   │   ├── interactionCreate // Configure command prefixes before execution
│   │   ├── guildMemberAdd
│   ├── db
│   │   ├── mongoose        // Ensure the folder name matches the database property
├── .env                     // Environment variables
├── index.js                 // Root application file
└── package.json
```


## example for the command 

```js

export default {
    command:{
        name: 'ping',
        description: 'Replies with the bot ping!'
    },
    callback: async ({client, eventArg}) => {

        await eventArg.deferReply();

        const reply = await eventArg.fetchReply();
        const ping = reply.createdTimestamp - eventArg.createdTimestamp;

        eventArg.editReply(
        `Pong! Client ${ping}ms | Websocket: ${client.ws.ping}ms`
        );

        return
    },
};

```

or 

```js 

import { SlashCommandBuilder } from 'discord.js';

export default {
    command: new SlashCommandBuilder()
        .setName('ping')
        .setDescription('Replies with the bot ping!'),
    async execute({eventArg}) {
        await eventArg.deferReply();

        const reply = await eventArg.fetchReply();
        const ping = reply.createdTimestamp - eventArg.createdTimestamp;

        await eventArg.editReply(
            `Pong! Client ${ping}ms | Websocket: ${eventArg.client.ws.ping}ms`
        );
    },
};

```

note: you can use normal export {} or default. but for export u cannot import greater than one object or variable

```js 

import { SlashCommandBuilder } from 'discord.js';

const command = {
    command: new SlashCommandBuilder()
        .setName('ping')
        .setDescription('Replies with the bot ping!'),
    async execute({eventArg}) {
        await eventArg.deferReply();

        const reply = await eventArg.fetchReply();
        const ping = reply.createdTimestamp - eventArg.createdTimestamp;

        await eventArg.editReply(
            `Pong! Client ${ping}ms | Websocket: ${eventArg.client.ws.ping}ms`
        );
    },
};

export {
    command
}

```

## params for the callback function

```js

callback: ({ client, eventArg, command, db }) // this all u can get with singgle callback func  

```

```sh

client= client objek from Client() instance discord.js 
eventArg= eventArg for interactionCreate 
command= your object command from ur export except callback function 
db= your object db instance if u set db path from eventHandlers 

```
 
### example command params

```js

export default {
    command: new SlashCommandBuilder()
        .setName('ping')
        .setDescription('Replies with the bot ping!'),
    async callback({eventArg, db, command}) {
        await eventArg.deferReply();
         
        console.log(command.devOnly)
        console.log(command.someRandomVar) 

        db.mongoose.user // this is ur user schema/model, the mongoose name is the prefix name from the name folder db/mongoose

        const reply = await eventArg.fetchReply();
        const ping = reply.createdTimestamp - eventArg.createdTimestamp;

        await eventArg.editReply(
            `Pong! Client ${ping}ms | Websocket: ${eventArg.client.ws.ping}ms`
        );
    },
    devOnly: true, 
    someRandomVar: "bla bla bla",
    ring: false // or whatever you want to
};

```

note: params command object is gonna go trough the events interactionCreate folder first (if exist) before reaching the callback function so you can set prefix or middleware before the callback function is called