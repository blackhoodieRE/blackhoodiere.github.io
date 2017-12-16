---
layout: single
title: "BlackHoodie IRC101"
exerpt: "Reverse Engineering Workshop for women"
tags: [IRC101, security, malware, BlackHoodie]
date: 2017-12-15 00:00:00 -0500
---

What is that thing ?
---------------------------
It stands for "Internet Relay Chat". It's basically a bunch of servers hosting channels where people can chat together.

Okok, how do I connect ?
-----------------------------------
First, you need a client, some examples depending on your platform:
* Windows:
  - [HexChat](https://hexchat.github.io/)
* Linux:
  - for a graphical interface, see [HexChat](https://hexchat.github.io/),
  - for a CLI interface [Irssi](https://irssi.org/) or [WeeChat](https://weechat.org/) (with eponymous packages on most Linux systems, ask your favorite package manager)
* Mac:
  - [Adium](https://www.adium.im/)
* Android:
  - [AndChat](https://play.google.com/store/apps/details?id=net.andchat.donate) is a popular client
* iOS: ?
* webApp:
  - [WebChat from Freenode](https://webchat.freenode.net/) if you don't want to install a "heavy" client

Next, you need to connect to an IRC network, here we will connect to Freenode. Freenode is one of the most popular network and might already be included in your client.

If not, you can add it with the following parameters:

```
server name: freenode
server address: chat.freenode.net
port: 6667
```


I'm connected ! Now what ?
---------------------------------------
Once you're connected, you need to join some channels, for example #blackhoodie:

> /join #blackhoodie \<password\>

Now you should have 2 tabs open:
* a tab named "freenode" where you can set settings for the freenode network
* a tab "#blackhoodie" where people should answer if you talk.

Most graphical IRC clients display:
* the list of channels you joined on the left
* the content of the current channel in the center
* the list of people currently on this channel on the right

ps: you can join many more channels !

That's all, you can talk all you want now, as long as it's on the #blackhoodie tab :D

Oh and keep in mind that, even though IRC is a synchronous messaging platform, people might be away from keyboard :)

Some optional stuff
============

I want to send a private message to someone
----------------------------------------------------------------
>/query \<nickname\>

This will open a new tab with a private conversation with that user only

I want to change my nickname
-------------------------------------------
>/nick \<new_nickname\>

I want to keep my nickname
---------------------------------------
To register your nickname, you need to talk to nickserv, the Freenode bot handling this
>/msg nickserv register \<password\> \<email\>

Once your nickname is registered, you can identify yourself with this command
>/msg nickserv identify \<password\>

-- [Kylma](https://twitter.com/_kylma)

PS: Original post [here](http://kylma.fr/irc101.html)
