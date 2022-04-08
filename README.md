# sduedu
university 
import logging
from time import sleep

import telegram
from telegram.error import NetworkError, Unauthorized

UPDATE_ID = None

def echo(bot):
    """Эхо сообщение отправленное пользователем."""
    global UPDATE_ID
    # Запрашиваем обновление после последнего update_id
    for update in bot.get_updates(offset=UPDATE_ID, timeout=10):
        UPDATE_ID = update.update_id + 1

        # бот может получать обновления без сообщений
        if update.message:
            # не все сообщения содержат текст
            if update.message.text:
                # Ответ на сообщение
                update.message.reply_text(f'ECHO: {update.message.text}')


if __name__ == '__main__':
    """Запускаем бота."""
    global UPDATE_ID
    # Токен авторизации бота Telegram
    bot = telegram.Bot('TOKEN')

    # получаем первый ожидающий `update_id`
    try:
        UPDATE_ID = bot.get_updates()[0].update_id
    except IndexError:
        UPDATE_ID = None

    logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')

    while True:
        try:
            echo(bot)
        except NetworkError:
            sleep(1)
        except Unauthorized:
            # Пользователь удалил или заблокировал бота.
            UPDATE_ID += 1
