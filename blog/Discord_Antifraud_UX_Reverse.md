# Discord Antifraud Reverse Engineering — Case Study

One of my jobs involved developing financial antifraud systems.

To put it simply, the role of antifraud is to protect a company and its users
from malicious activity.

Today my friend was unable to verify his Discord account from country A using country B phone number.

The UX made it look as if the phone number were invalid, which was clearly not
true. From my experience as a developer, I remembered:

![Discord Antifraud error msg](../assets/discord_antitfraud_weird_err.jpg)

You actively try to hide the rules of your fraud-detection system, and even the
existence of the system itself, from bad actors.
E.g. you never give errors like 'hmm... our AF score 0.7 says you are a bad dude, we don't allow you to do something'.

When we used an IP address from country A with a phone number from country A,
everything worked just fine 😁
This is a mere drop in the ocean, and I believe Discord engineers have a
very sophisticated scoring system with ML models.

However, it was nice to uncover one of the rules as it was actually implemented.

## Conclusions

### Sad ones

- This kind of UX wasn't helpful. I'm not sure antifraud should deceive
  legitimate users in such a bad way :( It worked for my friend, but this
  clearly doesn't work against professional fraud specialists. Based on this,
  I'm generally against this kind of UX in Avito.

- For any kind of GeoBlocks 😶, please allow people to use different IP
  addresses and phone numbers, and respect their privacy. It's kind of insane
  that Discord doesn't allow digital nomads to register there. People in
  Thailand don't use their home phone numbers all the time, do they?

### Happy ones

- Now we know super specific details of Discord Antifraud.

- Reverse-engineering antifraud systems is, well, really fun. I've sometimes
  wished I were developing an anti-detect browser instead, because this is so
  much harder and more fun.

## Side Note

Discord has one of the [best articles for backend engineers ever](https://discord.com/blog/how-discord-stores-trillions-of-messages).
I consider it a must-read for any engineer interested in database design and
scaling.
