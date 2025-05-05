- 👋 Hi, I’m @ozodbek1808
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
ozodbek1808/ozodbek1808 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Updater, CommandHandler, CallbackQueryHandler, CallbackContext

# Telegram bot tokenini kiritish
TOKEN = '7791464937:AAHDq-lWDvQktQPL-MPpQ63YjAVYS-UK914'

# Test savollari
questions = [
    {"question": "Какой раздел языкознания изучает звуки речи?", "answers": ["A) Синтаксис", "Б) Лексика", "В) Фонетика", "Г) Морфология"], "correct": "В) Фонетика"},
    {"question": "Какой раздел языкознания занимается изучением слов и их значений?", "answers": ["A) Фонетика", "Б) Лексика", "В) Стилистика", "Г) Синтаксис"], "correct": "Б) Лексика"},
    {"question": "Что такое антонимы?", "answers": ["A) Слова одинаковые по звучанию", "Б) Слова с одинаковым значением", "В) Слова с противоположным значением", "Г) Слова с разной формой, но одинаковым значением"], "correct": "В) Слова с противоположным значением"},
]

# Foydalanuvchiga testni boshlash
def start(update: Update, context: CallbackContext):
    update.message.reply_text("Salom! Testni boshlash uchun /test komandasini kiriting.")

# Testni boshlash
def test(update: Update, context: CallbackContext):
    user_id = update.message.chat_id
    context.bot.send_message(user_id, "Testni boshlaymiz! Savollarni diqqat bilan o'qing.")

    # Savollarni yuborish
    ask_question(update, context, 0)

# Savolni yuborish va tugmalarni qo'shish
def ask_question(update: Update, context: CallbackContext, question_index: int):
    question_data = questions[question_index]
    question_text = question_data['question']
    answers = question_data['answers']

    keyboard = [[InlineKeyboardButton(answer, callback_data=answer)] for answer in answers]
    reply_markup = InlineKeyboardMarkup(keyboard)

    update.message.reply_text(question_text, reply_markup=reply_markup)

# Javoblarni tekshirish
def button(update: Update, context: CallbackContext):
    query = update.callback_query
    query.answer()
    
    user_answer = query.data
    question_index = int(query.message.text.split()[1])  # Savolning indeksi

    # Javobni tekshirish
    correct_answer = questions[question_index]['correct']
    if user_answer == correct_answer:
        query.edit_message_text(text=f"Javobingiz to'g'ri! Savol: {questions[question_index]['question']}")
    else:
        query.edit_message_text(text=f"Javobingiz noto'g'ri! To'g'ri javob: {correct_answer}\nSavol: {questions[question_index]['question']}")
    
    # Keyingi savolni yuborish
    next_question_index = question_index + 1
    if next_question_index < len(questions):
        ask_question(query, context, next_question_index)

# Botni ishga tushirish
def main():
    updater = Updater(TOKEN, use_context=True)
    dispatcher = updater.dispatcher

    # Bot uchun komandalar
    dispatcher.add_handler(CommandHandler("start", start))
    dispatcher.add_handler(CommandHandler("test", test))
    dispatcher.add_handler(CallbackQueryHandler(button))

    # Botni ishga tushurish
    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
