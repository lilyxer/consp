## [telebot](https://pypi.org/project/pyTelegramBotAPI/) / [rus](https://pytba.readthedocs.io/ru/latest/index.html)

```bash
pip install pyTelegramBotAPI
```

- получаем токен у [@BotFather](https://t.me/BotFather)
- самый простой телебот который здоровается стобой на любое сообщение
```python
import telebot 
token = ''
bot = telebot.TeleBot(token)
@bot.message_handler(content_types=["text"])
def echo(message):
    bot.send_message(message.chat.id, f"Hi, {message.json['chat']['first_name']}")
bot.polling(none_stop=True)
```

- @bot.message_handler обработчик событий, в аргументах принимает тип данных
```python # типы 
commands=['start', 'help']      # реакция на /start or /help
chat_types['private']           # обработчик по типу чата, приватный, групповой
content_types=['text', 'photo'] # реакция на любой текст или фото
regexp=r''                      # обаботчик отреагирует если есть вхождениепо регулярке
func=lambda message: message.text == '8'   # обычная функция для обработки, 1аргумент=message
'and'                           # несколько аргументов в обработчике 
'or'                            # несколько обработчиков
```

- message.keys доступ по точечной нотации
```bash
|content_type                   # информация о сообщении
-- | from_user                  # информация об отправителе first_name, last_name, username
-- | date                       # информация о дате unix формат
-- | chat                       # информация о чате id, type, title
-- | json                       # короткаия иформация message_id, from
    -- | from                   # id, bot, first_name, last_name, username
    -- | chat                   # id, first_name, last_name, username, type
    -- | date                   # информация о времени unix
    -- | text                   # само сообщение
    -- | entities               # у комманд offset, length, type

message.chat.id                 # идентефикатор чата с ботом
message.from_user.id            # идентефикатор пользователя
message.from_user.username      # ник пользователя
```

- message.methods
```python
send_message(чат, объект)       # отправка сообщений
parse_mode='HTML' / 'Markdown'  # для форматирования текста
send_...                        # отправка других типов, см. TeleBot.send_
reply_to(message, 'сообщение')  # ответ с цитатой
```

- отправка файлов
```python
# на хосте
with open('file.mp4', 'rb') as file:        # для каждого типа файла свой метод
    bot.send_video(message.chat.id, file)   # TeleBot.send_ / AsyncTeleBot.send_
# по ссылке 
bot.send_video(message.chat.id, 'link')
```

- клавиатуры
```python
# ReplyKeyboardMarkup - кнопки клавиатуры
kb = types.ReplyKeyboardMarkup(resize_keyboard=True, row_width=2, one_time_keyboard=True)   # сама клавиатура
btn1 = types.KeyboardButton(text='Arrizo8')     # кнопки
btn2 = types.KeyboardButton(text='Tiggo 4 PRO')
btn3 = types.KeyboardButton(text='Tiggo 7 PRO MAX')
btn4 = types.KeyboardButton(text='Tiggo 8 PRO MAX')
kb.add(btn1, btn2, btn3, btn4)   # добавляем кнопки
bot.send_message(chat_id=message.chat.id, reply_markup=kb)      # возвращаем клавиатуру
# InLineKeyboardMarkup - кнопки в сообщении
kb = types.InLineKeyboardMarkup()
```