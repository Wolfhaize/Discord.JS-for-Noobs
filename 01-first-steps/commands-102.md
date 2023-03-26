# Command Responses
Your bot has to respond whenever a user runs a command with it. If it doesn't respond, the user sees that `application did not respond` message, and even though your bot may do something in the background, no one really wants to see that.

In our [basic ping command](https://github.com/Wolfhaize/Discord.JS-for-Noobs/blob/main/01-first-steps/commands/basicping.js):

```javascript
1  const { SlashCommandBuilder } = require('discord.js');
2 
3  module.exports = {
4	data: new SlashCommandBuilder()
5		.setName('ping')
6		.setDescription('Replies with Pong!'),
7	async execute(interaction) {
8	await interaction.reply('Pong!');
9	},
10 };
```

*Line 8* is our response, a simple chat input reply, visible to anyone.

We can make this an *ephemeral response*, aka one that only the user who ran the command can see, by editing line 8 this way:
```javascript
await interaction.reply({ content: 'Pong!', ephemeral: true });
```

## Editing Responses
You can also edit your bot's reponse by replying first and then editing the reply afterwards:

```javascript
await interaction.reply('Pong!');
await interaction.editReply('Edited pong!');
```

You may add a delay to tell the bot how long to wait before making the edit:

```javascript
await interaction.reply('Pong!');
await wait(2000); //milliseconds
await interaction.editReply('Edited pong!');
```


## Deferring Responses
One of the most painful things you might encounter is an error that says `Unknown interaction`.

This mostly appears because Discord requires your bot to respond within *three seconds* to prove that it received the slash command. However there are commands that will take longer than that time to be processed. This is where deferring responses comes in to save the day. 

If we are to take the above ping command and defer its reply, we have:

```javascript
await interaction.deferReply();
await wait(4000);
await interaction.editReply('Pong!');
```
On Discord, if you try to run this command you'll see the `'Bot Name' is thinking` appear, until after the four seconds of delay, and then finally the reply.

This way even with a set delay of 4 seconds, Discord won't throw a fit.

if you're deferring a response and want it to be ephemeral:

```javascript
await interaction.deferReply({ ephemeral: true });
await wait(4000);
await interaction.editReply('Pong!');
```

## Follow Up Responses
This is for if you want to send additional responses to the intial response.

```javascript
await interaction.reply('Pong!');
await interaction.followUp('Pong again!');
```

You can also make the follow up ephemeral:

```javascript
await interaction.reply('Pong!');
await interaction.followUp({ content: 'Pong again!', ephemeral: true });
```

**Note**

* After the initial response, an interaction token is valid for 15 minutes, so this is the timeframe in which you can edit the response and send follow-up messages.

* You also cannot edit the ephemeral state of a message, so ensure that your first response sets this correctly when using `editReply()`.


## Deleting Responses
This is for if you want to delete your intial bot's repsonse.

```javascript
await interaction.reply('Pong!');
await interaction.deleteReply();
```

## Fetching repsonses
This is for in case you need your bot's response for something else in your code.

```javascript
await interaction.reply('Pong!');
const botResponse = await interaction.fetchReply();

//do stuff with botResponse
```
