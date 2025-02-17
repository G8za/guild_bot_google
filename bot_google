import logging
import gspread
from oauth2client.service_account import ServiceAccountCredentials
from telegram import Update, ForceReply
from telegram.ext import Application, CommandHandler, MessageHandler, filters, CallbackContext

# Настройка логов
logging.basicConfig(level=logging.INFO)

# Подключение к Google Sheets
scope = ["https://spreadsheets.google.com/feeds", "https://www.googleapis.com/auth/drive"]
creds = ServiceAccountCredentials.from_json_keyfile_name("google_keys.json", scope)  # JSON-файл ключа
client = gspread.authorize(creds)

# Открываем таблицу
spreadsheet = client.open("User Data")  # Имя таблицы
worksheet = spreadsheet.sheet1

# Функция /start
async def start(update: Update, context: CallbackContext) -> None:
    await update.message.reply_text("Привет! Напиши свой ник и данные в формате: Ник - Данные")

# Функция обработки сообщений
async def handle_message(update: Update, context: CallbackContext) -> None:
    text = update.message.text
    try:
        nick, data = map(str.strip, text.split("-", 1))  # Разделяем ник и данные
    except ValueError:
        await update.message.reply_text("Ошибка! Вводи данные в формате: Ник - Данные")
        return

    # Проверяем, есть ли ник в таблице
    cell = worksheet.find(nick)
    if cell:
        worksheet.update_cell(cell.row, cell.col + 1, data)  # Записываем в соседнюю ячейку
    else:
        worksheet.append_row([nick, data])  # Добавляем новую строку

    await update.message.reply_text(f"Данные для {nick} сохранены!")

# Запуск бота
def main():
    application = Application.builder().token("YOUR_BOT_TOKEN").build()
    application.add_handler(CommandHandler("start", start))
    application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))
    
    application.run_polling()

if __name__ == '__main__':
    main()
