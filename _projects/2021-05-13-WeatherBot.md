---
layout: post
title: Army CAT 1 Weather Bot
excerpt: Telegram Bot to notify user when there is CAT 1 Lightning Risk Alert for a specific area
categories: NS, Telegram, Python
---

Introduction
------

I was appointed as Safety IC for 2 weeks during my OCS service term. One of my duties was to keep track of the CAT 1 status to ensure the safety of my platoon. The Army has a telegram channel that will post updates of the CAT 1 status for the whole Singapore which comprised of 20+ sectors. Since I only cared about 1 sector, I decided to make a telegram bot to provide weather updates for the sector where my camp was located.

### Methodology

The bot doesn't really do very much. It can be broken down into 3 functions. 

1. Read the data from the existing telegram channel
2. Extract the relevant data
3. Notify the user

### Step 1: Reading the Data

After spending some time on stackoverflow, and reading the telegram api documentation, I found out you can listen to all messages received by a user. To make coding a little easier, I decided to use the [Telethon library](https://docs.telethon.dev/en/latest/).

```python
@client.on(events.NewMessage(pattern='(?i).*Hello'))
   async def handler(event):
      await event.reply('Hey!')
``` 

From the example code in the telethon documentation, we can modify the pattern parameter to ensure the messages read are from the CAT 1 Telegram Channel. Luckily, the messages sent in the channel follow a specific format. We can take advantage of this, filtering the received messages to those containing `[CAT Status Update]` (the first line of all messages send in the channel).

```python
@client.on(events.NewMessage(pattern='\[CAT Status Update\]'))
async def handler(event):
   print('received cat status')
   msg = event.message.message
   print(msg)
```

### Step 2: Extracting relevant data

The data we are working with is just text. In python, we can easily check if some text `x` is contained within another text `y` by doing

```python
if x in y: 
```

However there is an edge case that cause this method to fail. Since the message also contains timings in 24 hour format, it is possible that the timings may cause a false positive. An example of this would be

```
[CAT Status Update] ⚡️
CAT 1:
(0930-1020)
09
```

The above message passes the condition because `02` is contained within the timing `1020`. While we could work around this edge case by implementing special checks, I thought it might be more elegant to do it with regex. 


With the help of [regexr.com](regexr.com) to test my pattern, I came up with

`pattern = '(02)([,\ \n]|$)'`

The first capture group `(02)` finds the relevant sector. The second capture group `([,\ \n]|$)` ensures that the subsequent character after the sector is either a comma, space, newline, or end of text. 

Next, we also want to extract the timings of the CAT Status update. We can do this easily by looking at the line right before the sector. 

```python
tmp = msg.split('\n')
print(tmp)
for x in range(len(tmp)):
if bool(re.search(pattern, tmp[x])):
  time = tmp[x-1]
```

### Notifying the User

We can use the telegram API to send messages on behalf of the user, however, for the user to receive a notification, someone else needs to send a message to them. We can take advantage of bots to acheive this. Following the steps on the telegram bot API, we create a simple bot using `@BotFather`. 

To protect the identity of my friends who I may share this bot with, I used a telegram channel as the medium through which messages are sent. After creating the channel and ensuring the bot is an admin, we extract the `chat_id` of the channel by forwarding a message from that channel to `@JsonDumpBot`. Now we just need to make a request to the api for the bot to send the message to the channel. 

```python
def notifyStatus(status):
  base = 'https://api.telegram.org/bot'
  payload = '/sendMessage?text=' + status + '&chat_id=' + chat_id
  url = base + bot_token + payload
  res = requests.get(url)
  return res
``` 

We also need to ensure that the user is notified once CAT 1 is cleared. We may do so using a global variable to alert the user when the sector is no longer mentioned in the messages. I chose not to use `if else` because that would create unnecessary spam which defeats the purpose of the bot. 

### Code

```python
from telethon import *
import asyncio
import requests
import re

api_id = [REDACTED]
api_hash = "[REDACTED]"
client = TelegramClient('anon', api_id, api_hash)

bot_token = '[REDACTED]'
chat_id = '[REDACTED]'

cleared = False

async def main():
	while True:
		await asyncio.sleep(1)

def notifyStatus(status):
	base = 'https://api.telegram.org/bot'
	payload = '/sendMessage?text=' + status + '&chat_id=' + chat_id
	url = base + bot_token + payload
	res = requests.get(url)
	return res
    
@client.on(events.NewMessage(pattern='\[CAT Status Update\]'))
async def handler(event):
	print('received cat status')
	global cleared
	print(cleared)
	msg = event.message.message
	print(msg)
	pattern = '(02)([,\ \n]|$)'
	if bool(re.search(pattern, msg)):
		cleared = False
		tmp = msg.split('\n')
		print(tmp)
		for x in range(len(tmp)):
			if bool(re.search(pattern, tmp[x])):
				time = tmp[x-1]
				print("found")
          
		notifyStatus("Sector 2 CAT 1 " + time)
	elif cleared == False:
		cleared = True
		notifyStatus("Sector 2 CAT 1 Cleared")

with client:
	client.loop.run_until_complete(main())
```


### Obstacles

Although it is explicitly mentioned on the first few lines of the Telegram API docs, it took me a while to understand and differentiate between the Bot API and the Telegram API. I don't use telegram very often, and I don't have much experience interacting with telegram bots. So when I told myself I wanted to create a bot, I instantly looked at the Telegram Bot API. As stated on the API home page, 

> Telegram Bots are special accounts that do not require an additional phone number to set up. These accounts serve as an interface for code running somewhere on your server.

The key thing to note was that bots were simply an interface for code to run. I wanted them to listen to the CAT 1 channel, but subscribing to a channel is not a function in the bot API. Typically, users have initiate a conversation with the bot with `/start`. Bots liten to commands or messages send to them directly. 