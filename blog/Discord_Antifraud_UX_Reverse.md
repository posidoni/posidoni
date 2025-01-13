# Discord Antifraud Reverse Engineering — Case Study

One of my jobs was developing financial antifraud.

To put it simply, the role of antifraud is to protect company/users from any form of malicious activity.

Today my friend was unable to verify his Discord account from country A using country B phone number.

The UX was like the phone is invalid which was clearly not true, and from my dev experience I remember:

![Discord Antifraud error msg](../assets/discord_antitfraud_weird_err.jpg)

You actively try to hide rules of your fraud-detection system and hide even the very existence of it from bad guys.
E.g. you never give errors like 'hmm... our AF score 0.7 says you are a bad dude, we don't allow you to do something'.

When we used IP of country A with country A phone, everything worked just fine 😁
Alright, this is a mere drop in the ocean, and I believe Discord engineer have a
very sophisticated scoring system with ML models.

However, it was nice to uncover one of the rules as is literally.

## Conclusions

### Sad ones

- This kind of UX wasn't helpful, I'm not sure antifraud should deceive
legitimate users in a such a bad way :( However, it worked for my friend,
but this clearly doesn't work for professional fraud specialists. Based on this,
I'm generally against this kind of UX in Avito.

- Any kind of GeoBlocks such 😶, please, allow people to use any IP/phones/etc.,
and respect privacy. It's kind of insane Discord doesn't allow digital nomads to
register there. Like people in Thailand doesn't use their home phone, really?

### Happy ones

- Now we know super specific details of Discord Antifraud.

- Reverse engineering antifrauds is, well, really fun. I've sometimes wish I
was developing some kind of anti-detect browser instead of antifraud, because
this is so much harder & fun.

## Side Note

Discord has one of the [best articles for backend engineers ever](https://discord.com/blog/how-discord-stores-trillions-of-messages).
I name this as a must read for any engineer interested in db design/scaling.

