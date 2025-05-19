# Листинг программы

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