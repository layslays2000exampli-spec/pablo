# pablo
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
	token := os.Getenv("DISCORD_TOKEN")MTQ4Mzk5MTIxNDI4MDkzNzU0Mw.GXJWbS.2NnsRqOtydf8DTY9RpLH7wTIp49hs_SnrRjqBk
	
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
}

