# Discord.js For Noobs

## Intro

If you're reading this, it probably means you want to learn how to make a bot with discord.js. Awesome! You've come to the wrong place. You're better off checking with the docs at [Discord.js](https://discordjs.guide/#before-you-begin), cause the people there actually know what they're talking about.

But if you choose to stay, this guide will show(hopefully teach) you how to:

* Get a basic shell of your bot ready and working.
* How to improperly create, organize, and expand on your commands.
* Simple explanations and examples regarding popular topics (e.g. reactions, embeds, roles);

# Before you begin...


Alright, making a bot is cool and all, but there are some prerequisites to it. To create a bot with discord.js, you should have a fairly decent grasp of JavaScript itself. But you don't, so let's get into it!

However its much better if you have an idea of what's going on, so you don't take hours to solve small issues that should take seconds to fix.


The original Discord.Js guide page gives you a bunch of links to help get started with Javascript, but these three have been particularly useful in my ongoing journey as a beginner, with their pros and cons included.

* Google - It has basically everything, you just have to know where to look/what to ask for.
* [MDN's JavaScript guide and full documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript) - Doesn't provide a lot of examples, but explains things in a way that's understandable.
* [Modern Javascript Tutorial](https://github.com/javascript-tutorial/en.javascript.info/tree/master/1-js) - I literally have a copy of this repo downloaded so I can look through it anytime. I highly suggest this one.


# Requests/Issues with this guide.

Feel free to create a new [issue here](https://github.com/Wolfhaize/Discord.JS-for-Noobs/issues), if you want to correct me somewhere, or something's not quite going as I say it should.

Or you can create a new [pull request here](https://github.com/Wolfhaize/Discord.JS-for-Noobs/pulls), if you want to contribute to this guide. 

If you've never created a pull request before, [try it here first](https://github.com/Wolfhaize/Github-contribution-for-Noobs).


# Installing Stuff
I'll assume this part is pretty direct in the original docs. So I'll skip it.

Also all of this is based on if you're using [Visual Studio Code](https://code.visualstudio.com/) as an IDE. I highly suggest it for anyone who wants to stay sane while writing code.


If you've gotten this far, you should have your bot already in a discord server, and with all requirements installed and setup as instructed, with the next step being writing code/getting it online.

# Actual Code Starts Here

First thing is to create a way for you to actually access your bot, and that's through its token. Go to the [Discord Developer Portal](https://discord.com/developers/applications), head on to the 'Bot' page for whichever bot you'll be working on, and copy the token there. 

**Note**
If your bot token gets leaked, reset it. Preferably, don't share it with anyone as it will grant them access to your bot. And don't share it anywhere public either, like GitHub.

## Create a `config.json` file
```json
{
	"token": "PUT-YOUR-TOKEN-HERE"
}
```

There's other alternatives to using a ```config.json``` file, but I've found it to be the easiest approach.


## Main file
Create a file named ```index.js```


```javascript
// Require the necessary discord.js classes
const { Client, Events, GatewayIntentBits } = require('discord.js');
const { token } = require('./config.json');

// Create a new client instance
const client = new Client({ 
    intents: [
        GatewayIntentBits.Guilds,
        GatewayIntentBits.GuildMessages,
	GatewayIntentBits.MessageContent,
	GatewayIntentBits.GuildMembers,
        ],
    });

// When the client is ready, run this code (only once)
// We use 'c' for the event parameter to keep it separate from the already defined 'client'
client.once(Events.ClientReady, c => {
	console.log(`Ready!`);
});

// Log in to Discord with your client's token
client.login(token);
```

If you compare with the original docs, you'll notice I made a few additions under ```intents```.

See, Discord has these things called [Privileged Intents](https://discord.com/developers/docs/topics/gateway#privileged-intents) that enable your bot to access particular 'sensitive' data. 

* GUILD_PRESENCES
* GUILD_MEMBERS
* MESSAGE_CONTENT

Before using these, you must enable them in your app's settings. In the Developer Portal, you can navigate to your app's settings then toggle the privileged intents on the Bots page under the "Privileged Gateway Intents" section. 

These intents will allow your bot to keep track of things like the content of particular messages, when new members join, number of members online...etc

I'm choosing to insert them into the code for you now, because during my struggles, I completely forgot these existed and spent way too long not knowing why some of my commands weren't working.

**Note**
If your bot reaches 100 servers, you'll have to apply for verification from Discord to keep access to these intents.

Next up, open your terminal, run ```node index.js``` to start the bot. If you see "Ready!" after a few seconds, stuff's working, and your bot should appear online on Discord.



That's it! You're basically a Discord Developer now. The next step will be creating commands for your bot.

**Tip**
Don't forget to go outside. It really helps with the frustration when things aren't working.





