from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Updater, CommandHandler, CallbackQueryHandler, CallbackContext

BOT_TOKEN = 8195392567:AAEKTuXy-yWklhezqBWzAK9pru0zN2K3WX0
CHANNEL_ID = -1002881718827
COMPULSORY_CHANNEL = "@ethicalhackingpr0"

VIDEO_LINKS = {
    "tutorials": "https://t.me/c/2658761133/11",
    "vip": "https://t.me/eyescub3"
}

def start(update: Update, context: CallbackContext):
    keyboard = [
        [InlineKeyboardButton("Join Channel", url=f"https://t.me/{COMPULSORY_CHANNEL[1:]}")],
        [InlineKeyboardButton("Verify Join", callback_data="check_join")]
    ]
    update.message.reply_text("Join the channel to access content:", reply_markup=InlineKeyboardMarkup(keyboard))

def check_join(update: Update, context: CallbackContext):
    query = update.callback_query
    user_id = query.from_user.id
    try:
        chat_member = context.bot.get_chat_member(CHANNEL_ID, user_id)
        if chat_member.status in ["member", "administrator", "creator"]:
            keyboard = [
                [InlineKeyboardButton("Tutorials", callback_data="tutorials")],
                [InlineKeyboardButton("VIP Content", url=VIDEO_LINKS["vip"])]
            ]
            query.edit_message_text("Choose content:", reply_markup=InlineKeyboardMarkup(keyboard))
        else:
            query.answer("Join the channel first!", show_alert=True)
    except Exception as e:
        query.answer("Error verifying membership.", show_alert=True)

def send_tutorial(update: Update, context: CallbackContext):
    query = update.callback_query
    query.answer()
    context.bot.send_message(chat_id=query.message.chat_id, text=f"Tutorial: {VIDEO_LINKS['tutorials']}")

def main():
    updater = Updater(BOT_TOKEN)
    dp = updater.dispatcher
    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(CallbackQueryHandler(check_join, pattern="^check_join$"))
    dp.add_handler(CallbackQueryHandler(send_tutorial, pattern="^tutorials$"))
    updater.start_polling()
    updater.idle()

if __name__ == "__main__":
    main()
