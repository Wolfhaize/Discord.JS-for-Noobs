# Slash commands, yay!

You'll want to use slash commands over the ordinary manual commands. Why?

Because Discord says so, and its kinda easier.

One of the biggest issues I've found while trying to find help with command creation is that a lot of examples/solutions to problems you face were done ages ago, and are probably outdated or simply not geared towards the ordinary discord.js bot. I'll try to update things here whenever changes are made that may disrupt the way the commands work.

## Create a Commands folder
For organisational purposes, create a folder named `commands` where the individual code for all your slash commands will go.


## Command Handling
In the old days, devs would just have one file with all their commands and excessively long `if/else` statements to determine what commands have been run by a user. Because we prefer sanity, we don't do that here.


### Edit your `index.js` file to:

```javascript
1 const fs = require('node:fs');
2 const path = require('node:path');
3 const { Client, Collection, Events, GatewayIntentBits } = require('discord.js');
4 const { token } = require('./config.json');
5
6 const client = new Client({ 
    intents: [
        GatewayIntentBits.Guilds,
        GatewayIntentBits.GuildMessages,
		GatewayIntentBits.MessageContent,
		GatewayIntentBits.GuildMembers,]});
7
8 client.commands = new Collection();
9 const foldersPath = path.join(__dirname, 'commands');
10 const commandFolders = fs.readdirSync(foldersPath);
11
12 for (const folder of commandFolders) {
13	 const commandsPath = path.join(foldersPath, folder);
14	 const commandFiles = fs.readdirSync(commandsPath).filter(file => file.endsWith('.js'));
15	   for (const file of commandFiles) {
16		const filePath = path.join(commandsPath, file);
17		const command = require(filePath);
18		if ('data' in command && 'execute' in command) {
19			client.commands.set(command.data.name, command);
20		} else {
21			console.log(`[WARNING] The command at ${filePath} is missing a required "data" or "execute" property.`);
22		}
23	}
24 }
25
26 client.once(Events.ClientReady, () => {
	console.log('Ready!');
 });

client.on(Events.InteractionCreate, async interaction => {
	if (!interaction.isChatInputCommand()) return;

	const command = client.commands.get(interaction.commandName);

	if (!command) return;

	try {
		await command.execute(interaction);
	} catch (error) {
		console.error(error);
		if (interaction.replied || interaction.deferred) {
			await interaction.followUp({ content: 'There was an error while executing this command!', ephemeral: true });
		} else {
			await interaction.reply({ content: 'There was an error while executing this command!', ephemeral: true });
		}
	}
});

client.login(token);
```
~~See I started numbering the lines, but then got lazy.~~


## Basic Ping Command
I'll use this example will show and explain the basics of slash commands and the different ways in which they can be manipulated.

### Ping.js
```javascript
1 const { SlashCommandBuilder } = require('discord.js');
2 
3 module.exports = {
4	data: new SlashCommandBuilder()
5		.setName('ping')
6		.setDescription('Replies with Pong!'),
7	async execute(interaction) {
8		await interaction.reply('Pong!');
9	},
10 };
```

*Line 1* calls on the SlashCommandBuilder class. Without this line, you're wasting time. 
It enables you to set the details of your slash command below.

* `.setName` : The name of your slash command, aka the text that the user types in to use that command.
* `setDescription` : The short description beneath the name of your command, preferably explaining what it does.

Other details that can be added to your command will be explained further ahead.

*Line 8* Determines your bot's reply, in this case the text in quotes: 'Pong!'


## Registering slash commands
Slash commands can be registered in two ways; in one specific guild(locally), or for every guild the bot is in(globally).

**Note** Guild means Discord server, if it wasn't obvious.

Preferably you want to deploy(make them available) your commands globally, but if you're testing something out, then you might not want it available to every one at that time.

Create a ```deploy-commands.js``` file in your project directory.

### Local deployment
If you're deploying to a single guild, you're going to have to specify which one it is first.

So in your `config.json` file, add:

```json
{
	"token": "TOKEN-HERE",
	"clientId": "CLIENT-ID-HERE",
	"guildId": "TEST-SERVER-ID-HERE"
}
```
Get your ClientID from the [Discord Developer Portal](https://discord.com/developers/applications), specifically under the application for your bot. (Discord will call this 'Application ID')

Get the serverID by turning on [Developer Mode](https://support.discord.com/hc/en-us/articles/206346498). If you don't have this on yet, why are you here-

##### `Deploycommands.js` 

```javascript
const { REST, Routes } = require('discord.js');
const { clientId, guildId, token } = require('./config.json');
const fs = require('node:fs');
const path = require('node:path');

const commands = [];
// Grab all the command files from the commands directory you created earlier
const commandsPath = path.join(__dirname, 'commands');
const commandFiles = fs.readdirSync(commandsPath).filter(file => file.endsWith('.js'));

// Grab the SlashCommandBuilder#toJSON() output of each command's data for deployment
for (const file of commandFiles) {
	const command = require(`./commands/${file}`);
	commands.push(command.data.toJSON());
}

// Construct and prepare an instance of the REST module
const rest = new REST({ version: '10' }).setToken(token);

// and deploy your commands!
(async () => {
	try {
		console.log(`Started refreshing ${commands.length} application (/) commands.`);

		// The put method is used to fully refresh all commands in the guild with the current set
		const data = await rest.put(
			Routes.applicationGuildCommands(clientId, guildId),
			{ body: commands },
		);

		console.log(`Successfully reloaded ${data.length} application (/) commands.`);
	} catch (error) {
		// And of course, make sure you catch and log any errors!
		console.error(error);
	}
})();
```

### Global deployment
This will make the commands available in all servers the bot is in and DMs with the bot, by default.

For global deployment, only this part of the `deploy-cimmands.js` file is changed.

```javascript
const data = await rest.put(
    //from Routes.applicationGuildCommands(clientId, guildId), to
			Routes.applicationCommands(clientId),
			{ body: commands },
		);
```


Run `node deploy-commands.js` to register your commands to the test server, or globally depending on what you decide to.

You can test the ping command you created now, and if it appears, then it's been deployed. If it works, even better!


It's safe to say that you could have gotten to this point using just the original docs alone. But this is where the docs throw you to the wolves to go off and learn how to build upon a basic ping command, with the idea that this was enough for you to know what the hell is going on.

The next stage will do exactly that, and show you additional details that you can apply to make your own commands.

Remember to run `node deploy-commands.js` whenever you make changes to a command so its updated. 

You can however create a command to do this directly, the original docs have a tutorial for that [here](https://discordjs.guide/additional-features/reloading-commands.html)

**Tip**
Get a close friend or two, so you can show off to them every single time your bot does something that works. Its pretty cool.
