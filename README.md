const Discord = require('discord.js');
const gopnik = new Discord.Client();
const YTDL = require("ytdl-core");

var prefix = "!";

var servers = {};

gopnik.on('ready', () => {
    gopnik.user.setActivity(prefix + 'ajuda')
    console.log('Estou vivo! Sem nenhum erro! by:__')
});

function play(connection, message) {
    var server = servers[message.guild.id];

    server.dispatcher = connection.playStream(YTDL(server.queue[0], {filter: "audioonly"}));

    server.queue.shift();

    server.dispatcher.on("end", function(){
        if(server.queue[0]) play(connection, message);
        else connection.disconnect();
    });
};
gopnik.on('message', message => {
    if (message.author.bot) return;
    if (message.content.startsWith(prefix + 'ping')){
        message.channel.sendMessage('Pong! Seu ping é de ``' + `${message.createdTimestamp - Date.now()}` + 'ms ``');
};

if(message.content.startsWith(prefix + "parar")){
    embed = new Discord.RichEmbed()
    .setThumbnail("https://icon-icons.com/icons2/933/PNG/512/rounded-pause-button_icon-icons.com_72587.png")
    .setTitle("Musica parada com sucesso!")
    .setColor('RED')
    message.channel.sendEmbed(embed)
    var server = servers[message.guild.id];

    if(message.guild.voiceConnection) message.guild.voiceConnection.disconnect();
};

if(message.content.startsWith(prefix + "pular")){
    embed = new Discord.RichEmbed()
    .setTitle("Musica pulada com sucesso")
    .setThumbnail("https://cdn1.iconfinder.com/data/icons/glyphie-1/40/button_skip_music_play_forward_end-512.png")
    .setColor('YELLOW')
    message.channel.sendEmbed(embed);
    var server = servers[message.guild.id];

    if(server.dispatcher) server.dispatcher.end();
};

if (message.content.startsWith(prefix + "tocar")){

    var args = message.content.substring(prefix).split(" ");
    

    if (!args[1]) {
        message.channel.sendMessage("Por favor coloque uma url da musica!");
        return;
    };
    
    if (!message.member.voiceChannel) {
        message.channel.sendMessage("Você prescisa estar conectado a um chat de voz!");
    };

    if (!servers[message.guild.id]) servers[message.guild.id] = {
        queue: []
    };

    var server = servers[message.guild.id];

    server.queue.push(args[1]);

    if (!message.guild.voiceConnection) message.member.voiceChannel.join().then(function(connection){
        play(connection, message);
    });
};

if (message.content.startsWith(prefix + "ajuda")){
    const embed = new Discord.RichEmbed()
    .setThumbnail('https://cdn.discordapp.com/avatars/407536802254028811/92719a477994767caefae8f6cafd2995.png?size=2048')
    .setAuthor("🖥 Meus comandos")
    .setTitle("🖋comandos casuais")
    .setDescription("⌨️!ping,!roleta,!criador")
    .addField("🎶comandos de musica", "⌨️!tocar,!pular,!parar")
    message.channel.send({embed});
};

if (message.content.startsWith(prefix + "criador")){
    embed = new Discord.RichEmbed()
    .setThumbnail('https://cdn.discordapp.com/avatars/406596101093982209/8f73c29a22e032e3a2e5cdc8c75ab681.png?size=2048')
    .setAuthor("Meu criador!")
    .setDescription("``Todos saúdem ao...`` __#8457!")
    message.channel.sendEmbed(embed)
};

if(message.content.startsWith(prefix + "roleta")){
    randomNumber = Math.floor(Math.random() * (4 - 1) + 1);
    if(randomNumber == 2){
        message.reply("**A roleta gira e gira...Você puxa o gatilho e...Morreu**");

    }
    else{
        message.reply("**A roleta gira,você aperta o gatinho e...Você não morre.Vitoria!**");

    }
};

let role = message.guild.roles.find("name", "./Hardcore_Slav")
if(message.member.roles.has(role.id) && message.content.startsWith(prefix + "delete")) {
    msgDel = 5;
    let numberMessages = parseInt(msgDel);
    message.channel.fetchMessages((numberMessages)).then(messages => message.channel.bulkDelete(messages));
    message.channel.send("Deletado com sucesso!")
};
});

gopnik.login('mandei o token no pv kkj');
