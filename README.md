const Discord = require("discord.js");
const client = new Discord.Client();
const config = require("./config.json");
const Cleverbot = require("cleverbot-node");
const clbot = new Cleverbot;
clbot.configure({botapi: "CC3naEi4lA4Sdvbi_9j1jsvRk8A"});
const newUsers = [];
const fs = require("fs");


client.login(config.token);

let responseObject = {
  "ayy": "Ayy, lmao!",
  "wat": "Say what?",
  "lol": "roflmaotntpmp",
  "Aaron": "amazing",
  "Lachy": "gone",
  "Nas": "the greatest",
  "James": "the best",
  "Team Report": "Aaron: amazing\nLachy: gone\nNas: the greatest\nJames: the best",
  "Assignment Status": "Going good",
  "access main security grid": "Ah, Ah, Ah! You didn't say the magic word!"
};

client.on("message", (message) => {
  if(responseObject[message.content]) {
    message.channel.send(responseObject[message.content]);
  } else
  if (!message.content.startsWith(config.prefix) || message.author.bot) return;

  if (message.content.startsWith(config.prefix + "ping")) {
    message.channel.send("pong!");
  } else
  if (message.content.startsWith(config.prefix + "test")) {
    message.channel.send("testing");
  } else
  if (message.content.startsWith(config.prefix + "sleep")) {
    message.channel.send("Done Sleeping");
  } else
  if (message.content.startsWith(config.prefix + "Games")) {
    message.channel.send("I have games for you such as:\n Slots\n Blackjack\n Dice Rolls\n");
  } else
  if (message.content.startsWith(config.prefix + "asl")) {
    const args = message.content.split(/\s+/g).slice(1);
    let age = args[0];
    let sex = args[1];
    let location = args[2];
    message.reply(`Hello ${message.author}, I see you're a ${age} year old ${sex} from ${location}. Wanna date?`);
  } 
});

client.on("message", message => {
  if (message.channel.type === "dm") {
    clbot.write(message.content, (response) => {
      message.channel.startTyping();
      setTimeout(() => {
        message.channel.send(response.output).catch(console.error);
        message.channel.stopTyping();
      }, Math.random() * (1 - 3) + 1 * 1000);
    });
  }
});

client.on("ready", () => {
  console.log("I am ready!");
});

let points = JSON.parse(fs.readFileSync("./points.json", "utf8"));
const prefix = "+";

client.on("message", message => {
  if (!message.content.startsWith(prefix)) return;
  if (message.author.bot) return;

  if (!points[message.author.id]) points[message.author.id] = {
    points: 0,
    level: 0
  };
  let userData = points[message.author.id];
  userData.points++;

  let curLevel = Math.floor(0.1 * Math.sqrt(userData.points));
  if (curLevel > userData.level) {
    // Level up!
    userData.level = curLevel;
    message.reply(`You"ve leveled up to level **${curLevel}**! Ain"t that dandy?`);
  }

  if (message.content.startsWith(prefix + "level")) {
    message.reply(`You are currently level ${userData.level}, with ${userData.points} points.`);
  }
  fs.writeFile("./points.json", JSON.stringify(points), (err) => {
    if (err) console.error(err);
  });

});

client.on("guildMemberAdd", (member) => {
  const guild = member.guild;
  if (!newUsers[guild.id]) newUsers[guild.id] = new Discord.Collection();
  newUsers[guild.id].set(member.id, member.user);

  if (newUsers[guild.id].size > 10) {
    const userlist = newUsers[guild.id].map(u => u.toString()).join(" ");
    guild.channels.get(guild.id).send("Welcome our new users!\n" + userlist);
    newUsers[guild.id].clear();
  }
});

client.on("guildMemberRemove", (member) => {
  const guild = member.guild;
  if (newUsers[guild.id].has(member.id)) newUsers.delete(member.id);
});


