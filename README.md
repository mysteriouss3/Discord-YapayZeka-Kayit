# Mys API Kullanımı

Bu repo, Mys API'nin Discord botu için kullanımını gösterir.

## Mys API Npm Paketi

Mys API'yi kullanmak için npm paketini [buradan](https://www.npmjs.com/package/mys-api.js) indirebilirsiniz.

## Komut ve Event

- KOMUT
```js

const { ActionRowBuilder,ButtonBuilder,ButtonStyle, } = require("discord.js");
const { MysAPI } = require('mys-api.js');

const row = new ActionRowBuilder()
            .addComponents(
                new ButtonBuilder()
                    .setCustomId("yapayzeka")
                    .setLabel("Otomatik Kayıt Ol")
                    .setStyle(ButtonStyle.Secondary)
                    .setEmoji('🏷'),
                new ButtonBuilder()
                    .setCustomId("names")
                    .setLabel("Isimlerimi Gör")
                    .setStyle(ButtonStyle.Primary)
                    .setEmoji('📝'),
            );


await message.channel.send({ 
content: "Mys-Api Tarafından Yapay Zeka Sisteminin Tanıtımı Sizi Kayıt Etmez Bu Panel Sadece Test Etmeniz Icin Yapılmıstır. 🎉",
components: [row] 
})

//bu yukarıdaki komut ile kanala menü gönderiyoruz...
//Kullanıcı Tıkladıgında Tüm Sunuculardaki Isimlerini Ve En çok kullanıdıgı isim yaş cinsiyetini görebilcek.
```
- EVENT
```js
const { Events, EmbedBuilder } = require("discord.js");
const { MysAPI } = require('mys-api.js') // BU MODULU İNDİRMEYİ UNUTMAYIN INDIRMEK ICIN (npm i mys-api.js) yazmanız gerekmektedir.


/*InteractionCreate Event =>*/ module.exports = async (interaction) => {
    const embed = new EmbedBuilder()
        .setAuthor({ name: interaction.guild.name, iconURL: interaction.guild.iconURL({ size: 128, extension: 'png' }) })
        .setFooter({ text: "By mysterious3" })
        .setColor('Random')

    if (interaction.isButton()) {
        const getData = await MysAPI.getUser(interaction.user.id) || null
        if (interaction.customId === "kayıt") {
            if (!getData) return interaction.reply({ content: "Kullanıcı Bulunamadı", ephemeral: true })
            if (getData.displayNames.length < 1) return interaction.reply({ content: "Kullanıcının isim kaydı bulunamadı", ephemeral: true })
            const user = getData.displayNames.map((x) => x).join("\n");
            return interaction.reply({ embeds: [embed.setDescription(`Kullanıcının isim kayıtları ;\n\`\`\`${user}\`\`\``)], ephemeral: true })
        }
        if (interaction.customId === "names") {
            if (!getData) return interaction.reply({ content: "Kullanıcı Bulunamadı", ephemeral: true })
            if (!(getData.TopName || getData.TopAge || getData.TopSex)) return interaction.reply({ content: "Kullanıcının kayıt bilgileri bulunamadı", ephemeral: true })
            if (getData.TopName === "bilinmiyor" || getData.TopAge === "bilinmiyor" || getData.TopAge === "bilinmiyor") return interaction.reply({ content: "Kullanıcının kayıt bilgileri bulunamadı", ephemeral: true })

            //Artık isim yaş ve cinsiyet şu kısımdan geliyor getData.TopName,getData.TopAge,getData.TopAge

            const OtherName = `İsim: ${getData.TopName}\nYaş: ${getData.TopAge}\nCinsiyet: ${getData.TopSex}`;
            return interaction.reply({ embeds: [embed.setDescription(`Yapay Zeka Ile Alınan Bilgileriniz ;\n\`\`\`${OtherName}\`\`\``)], ephemeral: true })
        }
    }
}
```
