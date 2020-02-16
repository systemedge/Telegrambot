# Telegrambot


import telebot

import config

import random

from telebot import types

bot = telebot.TeleBot(config.TOKEN)

@bot.message_handler(commands=['start'])

def welcome(message):

	sti = open('static/welcome.webp', 'rb')
	
	bot.send_sticker(message.chat.id, sti)

	# keyboard
	markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
	item1 = types.KeyboardButton("🎲 Liczba losowa")
	item2 = types.KeyboardButton("😊 Jak się masz?")

	markup.add(item1, item2)

	bot.send_message(message.chat.id, "Witam, {0.first_name}!\nJa - <b>{1.first_name}</b>, bot zaprojektowany jako świnka morska.".format(message.from_user, bot.get_me()),
		parse_mode='html', reply_markup=markup)

@bot.message_handler(content_types=['text'])

def lalala(message):

	if message.chat.type == 'private':
	
		if message.text == '🎲 Liczba losowa':
		
			bot.send_message(message.chat.id, str(random.randint(0,100)))
			
		elif message.text == '😊 Jak się masz?':

			markup = types.InlineKeyboardMarkup(row_width=2)
			item1 = types.InlineKeyboardButton("W porządku", callback_data='good')
			item2 = types.InlineKeyboardButton("Niezbyt", callback_data='bad')

			markup.add(item1, item2)

			bot.send_message(message.chat.id, 'Świetnie, jak się masz?', reply_markup=markup)
		else:
			bot.send_message(message.chat.id, 'Nie wiem, co odpowiedzieć 😢')

@bot.callback_query_handler(func=lambda call: True)

def callback_inline(call):

	try:
		if call.message:
			if call.data == 'good':
				bot.send_message(call.message.chat.id, 'To wspaniale 😊')
			elif call.data == 'bad':
				bot.send_message(call.message.chat.id, 'To się zdarza 😢')

			# remove inline buttons
			bot.edit_message_text(chat_id=call.message.chat.id, message_id=call.message.message_id, text="😊 Jak się masz?",
				reply_markup=None)

			# show alert
			bot.answer_callback_query(callback_query_id=call.id, show_alert=False,
				text="NINIEJSZA INFORMACJA TESTOWA!!11")

	except Exception as e:
		print(repr(e))

# RUN
bot.polling(none_stop=True)
