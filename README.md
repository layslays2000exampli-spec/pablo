# pablo
wsw
package main

import (
	"fmt"
	"os"
	"os/signal"
	"strings"
	"syscall"

	"github.com/bwmarrin/discordgo"
)

func main() {
	// ضع التوكن هنا أو في المتغيرات على الاستضافة
	token := os.Getenv MTQ4Mzk5MTIxNDI4MDkzNzU0Mw.GXJWbS.2NnsRqOtydf8DTY9RpLH7wTIp49hs_SnrRjqBk
	
	dg, err := discordgo.New("Bot " + token)
	if err != nil {
		fmt.Println("خطأ في الاتصال:", err)
		return
	}

	dg.AddHandler(messageCreate)

	// تفعيل الـ Intents لقراءة الأعضاء
	dg.Identify.Intents = discordgo.IntentsGuildMessages | discordgo.IntentsGuildMembers

	err = dg.Open()
	if err != nil {
		fmt.Println("خطأ في فتح الجلسة:", err)
		return
	}

	fmt.Println("البوت يعمل الآن... اضغط CTRL-C للخروج.")
	sc := make(chan os.Signal, 1)
	signal.Notify(sc, syscall.SIGINT, syscall.SIGTERM, os.Interrupt)
	<-sc

	dg.Close()
}

func messageCreate(s *discordgo.Session, m *discordgo.MessageCreate) {
	// منع البوت من الرد على نفسه
	if m.Author.ID == s.State.User.ID {
		return
	}

	// أمر الإرسال (مثال: !bc رسالتك هنا)
	if strings.HasPrefix(m.Content, "!bc ") {
		// التأكد أن المرسل هو صاحب البوت فقط (اختياري للأمان)
		// if m.Author.ID != "ايدي_حسابك_هنا" { return }

		msg := strings.TrimPrefix(m.Content, "!bc ")
		
		// جلب جميع أعضاء السيرفر
		members, _ := s.GuildMembers(m.GuildID, "", 1000)

		count := 0
		for _, member := range members {
			if member.User.Bot { continue } // تخطي البوتات

			// إنشاء قناة خاصة مع العضو
			channel, err := s.UserChannelCreate(member.User.ID)
			if err != nil {
				continue
			}

			// إرسال الرسالة
			_, err = s.ChannelMessageSend(channel.ID, msg)
			if err == nil {
				count++
			}
		}

		s.ChannelMessageSend(m.ChannelID, fmt.Sprintf("✅ تم إرسال الرسالة لـ %d عضو بنجاح!", count))
	}
}s.ChannelMessageSendComplex(m.ChannelID, &discordgo.MessageSend{
    Content: "@everyone " + message,
    AllowedMentions: &discordgo.MessageAllowedMentions{
        Parse: []discordgo.AllowedMentionType{discordgo.AllowedMentionTypeEveryone},
    },
})const { Client, GatewayIntentBits, SlashCommandBuilder, REST, Routes } = require('discord.js');

// اعددات البوت الأساسية
const client = new Client({ intents: [GatewayIntentBits.Guilds, GatewayIntentBits.GuildMessages, GatewayIntentBits.MessageContent] });

// --- ضع بياناتك هنا ---
const TOKEN = 'ضع_توكن_البوت_هنا';
const CLIENT_ID = 'ضع_ID_البوت_هنا';
// ----------------------

const commands = [
    // أمر السبام العادي
    new SlashCommandBuilder()
        .setName('spam')
        .setDescription('يرسل رسائل مكررة')
        .addStringOption(option => option.setName('message').setDescription('الرسالة').setRequired(true))
        .addIntegerOption(option => option.setName('count').setDescription('العدد').setRequired(true)),
    
    // أمر منشن الجميع
    new SlashCommandBuilder()
        .setName('mentionall')
        .setDescription('يرسل منشن للجميع')
        .addIntegerOption(option => option.setName('count').setDescription('عدد مرات التكرار').setRequired(true))
].map(command => command.toJSON());

// تسجيل الأوامر في الديسكورد
const rest = new REST({ version: '10' }).setToken(TOKEN);
(async () => {
    try {
        await rest.put(Routes.applicationCommands(CLIENT_ID), { body: commands });
        console.log('تم تفعيل الأوامر بنجاح!');
    } catch (error) {
        console.error(error);
    }
})();

// تنفيذ الأوامر
client.on('interactionCreate', async interaction => {
    if (!interaction.isChatInputCommand()) return;

    if (interaction.commandName === 'spam') {
        const message = interaction.options.getString('message');
        const count = interaction.options.getInteger('count'); [cite: 1, 2]
        await interaction.reply(`بدأ الإرسال: ${count} مرة.`);
        
        for (let i = 0; i < count; i++) {
            await interaction.channel.send(message);
            await new Promise(resolve => setTimeout(resolve, 1500)); // تأخير ثانية ونصف لتجنب الحظر [cite: 3]
        }
    }

    if (interaction.commandName === 'mentionall') {
        const count = interaction.options.getInteger('count'); [cite: 5]
        await interaction.reply(`بدأ منشن الجميع ${count} مرة...`); [cite: 6]
        
        for (let i = 0; i < count; i++) {
            await interaction.channel.send('@everyone');
            await new Promise(resolve => setTimeout(resolve, 2000)); // تأخير ثانيتين للمنشن [cite: 7]
        }
    }
});

client.login(TOKEN);
