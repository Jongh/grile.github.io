---
title: "python 텔레그램 봇 만들기 - 봇 생성과 봇 동작"
date: 2020-06-26 11:20:26 +0900
categories: python
tags: telegram bot
---

## 봇 생성과 봇 동작

### 봇 계정 만들기

- 텔레그램의 봇 계정은 텔레그램의 봇 생성 전용 봇을 이용하여 만들 수 있다. 해당 봇의 이름은 [BotFather](https://t.me/BotFahter)로 아래와 같은 프로필을 가지고 있다. 유사한 계정이 몇개 있으니 조심해야 한다.

![Bot Father Profile](https://jongh.github.io/assets/image/2020-06-26/botfather_profile.png)

- BotFather를 확인했으면 채팅을 시작한다. bot과 새로운 채팅을 시작하면 자동적으로 /start라는 명령을 보내게 되는데, 명령을 보내면 봇 생성에 대한 설명이 있는 채팅이 아래와 같이 출력된다.

![Bot Father Start](https://jongh.github.io/assets/image/2020-06-26/botfather_start.png)

- 명령이 다양하게 있는데, 봇을 만들기 위해서는 /newbot을 해야할 것 같다. /newbot을 이용해 생성을 시작하면 BotFather가 봇 생성을 위한 몇가지 질문을 한다.

![Bot Father Newbot](https://jongh.github.io/assets/image/2020-06-26/botfather_newbot.png)

- BotFather는 가장 처음 봇의 이름이 무엇인지 물어본다. 봇의 이름은 중복이 가능하니, 문제가 되지 않는 이름으로 잘 지어주면 된다.

- 다음 질문은 봇의 유저명으로, 우리가 ID라고 주로 부르는 명칭을 물어본다. 이 명칭은 유일해야 하며, 마지막이 bot으로 끝나야하는 조건이 있다. 따라서 유저명을 통해 계정이 일반 사용자 계정인지, 봇 계정인지 알 수 있는 것이다.

- 두가지 설정이 끝나면 봇을 인증할 수 있는 HTTP API를 보내준다. 해당 API는 봇 계정의 패스워드 같은 값이므로 비밀을 유지하며 잘 보관하면 된다.

### 파이썬 패키지 설치 및 봇 동작

- 텔레그램 봇을 만들기 위해서 사용한 패키지는 [python-telegram-bot](https://github.com/python-telegram-bot/python-telegram-bot)이다. 패키지를 설치하기 위해 터미널에서 아래와 같이 명령을 수행하자.

```bash
pip install python-telegram-bot --upgrade
```

- 패키지가 설치되었으면, 간단한 커맨드를 이용하여 봇이 동작할 수 있게 인사 명령을 받을 수 있는 스크립트를 구현해보자. 아래와 같이 스크립트를 실행하고 봇과 채팅을 시작하면 다음과 같은 작업을 할 수 있다.

```python
from telegram.ext import Updater, CommandHandler

tkn = 'bot_token' # HTTP API를 여기에 입력한다.

updater = Updater(tkn, use_context=True)

def sayhello(update, context):
    first_name = update.message.chat['first_name']
    last_name = update.message.chat['last_name']
    reply = '안녕? {0}{1}!'.format(last_name, first_name)
    update.message.reply_text(reply)

updater.dispatcher.add_handler(CommandHandler('hello', sayhello))
updater.start_polling()
updater.idle()
```

![My Bot Hello](https://jongh.github.io/assets/image/2020-06-26/mybot_hello.png)

- 다양한 봇의 사용방법은 [python-telegram-bot examples](https://github.com/python-telegram-bot/python-telegram-bot/tree/master/examples)에 있는 연습 소스들을 통해 알아볼 수 있다.
