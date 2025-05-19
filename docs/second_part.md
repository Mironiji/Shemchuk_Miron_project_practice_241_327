# 📸 Telegram-бот для поиска изображений через SerpApi

Этот бот позволяет пользователям отправлять поисковые запросы в Telegram, а в ответ получать изображения из Google с помощью SerpApi.

---

## 🔧 Установка зависимостей

Убедитесь, что у вас установлен Python 3. Затем установите необходимые библиотеки:

```bash
pip install pytelegrambotapi serpapi
```

---

## 🔑 Необходимые ключи

Вам потребуется:
- Telegram Bot Token (получите у @BotFather)!()[bot.jpg]
- SerpApi API Key (получите на https://serpapi.com/)

---

## 🧠 Принцип работы

1. Пользователь отправляет текстовый запрос в Telegram-бот.
2. Бот обрабатывает сообщение и формирует параметры для поиска изображений в SerpApi.
3. Выполняется HTTP-запрос к SerpApi.
4. Бот получает список изображений и отправляет пользователю первые из них.
!()[server.jpg]
---

## 💻 Код бота (Python)

```python
import telebot
from serpapi import GoogleSearch

# Укажите свои ключи
TELEGRAM_BOT_TOKEN = 'ВАШ_ТОКЕН_ОТ_TELEGRAM'
SERPAPI_API_KEY = 'ВАШ_КЛЮЧ_ОТ_SERPAPI'

bot = telebot.TeleBot(TELEGRAM_BOT_TOKEN)

@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
    bot.reply_to(message, "Привет! Отправь мне любой поисковый запрос, и я найду картинки!")

@bot.message_handler(func=lambda message: True)
def search_images(message):
    query = message.text.strip()
    
    params = {
        "engine": "google",
        "q": query,
        "tbm": "isch",
        "api_key": SERPAPI_API_KEY
    }

    try:
        search = GoogleSearch(params)
        results = search.get_dict()
        images = results.get("images_results", [])
        
        if not images:
            bot.reply_to(message, "Ничего не найдено 😢")
            return
        
        for img in images[:3]:  # Отправляем первые 3 изображения
            bot.send_photo(message.chat.id, img["original"])
    
    except Exception as e:
        bot.reply_to(message, f"Произошла ошибка: {e}")

bot.polling()
```

---

## ✅ Пример использования

1. Запустите скрипт.
2. Напишите боту что-нибудь вроде: `кот в очках`
3. Получите изображения прямо в чате Telegram.
!()[work.jpg]
---

## 📌 Примечания

- Убедитесь, что ваш токен и API ключ актуальны.
- Вы можете настроить количество отправляемых изображений, изменив `images[:3]` на нужное число.
- Не злоупотребляйте количеством запросов, чтобы не превысить лимит SerpApi.

---

## 📚 Источники

- [SerpApi Documentation](https://serpapi.com/)
- [PyTelegramBotAPI Docs](https://github.com/eternnoir/pyTelegramBotAPI)