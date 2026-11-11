import os
from flask import Flask
from threading import Thread
import telebot
from telebot import types

# ۱. راه‌اندازی وب‌سرور سبک برای جواب دادن به پورت رندر
app = Flask(__name__)

@app.route('/')
def home():
    return "BabyGram Bot is alive and running!"

def run_web():
    port = int(os.environ.get("PORT", 10000))
    app.run(host="0.0.0.0", port=port)

# ۲. راه‌اندازی ربات با توکن محیطی رندر
TOKEN = os.environ.get("TOKEN")
bot = telebot.TeleBot(TOKEN)

# دیکشنری متن‌ها برای زبان‌های مختلف
translations = {
    'fa': {
        'welcome': "لطفاً زبان خود را انتخاب کنید 👇\n\nسلام! خوش آمدید به بات بی‌بی‌گرام 🚀",
        'btn_fa': "🇮🇷 فارسی",
        'btn_en': "🇬🇧 English",
        'selected': "✅ زبان شما روی **فارسی** تنظیم شد. مینی‌اپ و بازی همینجا فعال می‌شود! 🎮",
        'default_reply': "پیام شما دریافت شد! برای شروع /start را ارسال کنید."
    },
    'en': {
        'welcome': "Hello! Welcome to BabyGram Bot 🚀\nPlease choose your language:",
        'btn_fa': "🇮🇷 فارسی",
        'btn_en': "🇬🇧 English",
        'selected': "✅ Your language has been set to **English**. The mini-app and game will be available here soon! 🎮",
        'default_reply': "Message received! Please send /start to begin."
    }
}

# ذخیره ساده زبان کاربرها در حافظه موقت
user_languages = {}

@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
    user_id = message.from_user.id
    lang = user_languages.get(user_id, 'fa')

    markup = types.InlineKeyboardMarkup(row_width=2)
    btn_fa = types.InlineKeyboardButton(translations[lang]['btn_fa'], callback_data='lang_fa')
    btn_en = types.InlineKeyboardButton(translations[lang]['btn_en'], callback_data='lang_en')
    markup.add(btn_fa, btn_en)

    bot.send_message(message.chat.id, translations[lang]['welcome'], reply_to_message_id=message.message_id, reply_markup=markup)

@bot.callback_query_handler(func=lambda call: call.data.startswith('lang_'))
def handle_language_selection(call):
    user_id = call.from_user.id
    if call.data == 'lang_fa':
        user_languages[user_id] = 'fa'
        bot.answer_callback_query(call.id, "زبان فارسی انتخاب شد.")
        bot.edit_message_text(translations['fa']['selected'], call.message.chat.id, call.message.message_id)
    elif call.data == 'lang_en':
        user_languages[user_id] = 'en'
        bot.answer_callback_query(call.id, "Language set to English.")
        bot.edit_message_text(translations['en']['selected'], call.message.chat.id, call.message.message_id)

@bot.message_handler(func=lambda message: True)
def echo_all(message):
    user_id = message.from_user.id
    lang = user_languages.get(user_id, 'fa')
    bot.reply_to(message, translations[lang]['default_reply'])

if __name__ == "__main__":
    # اجرای وب‌سرور در پس‌زمینه برای جلوگیری از ارور پورت رندر
    t = Thread(target=run_web)
    t.start()
    
    print("Bot is running with multi-language support and web server...")
    bot.infinity_polling()
    
