from aiogram import Bot, Dispatcher, types
from aiogram.utils import executor
import yt_dlp

TOKEN = "7667662544:AAEJ2f-6GDp-BIObURJIJkg6sddx5cnOb-w"

bot = Bot(token=TOKEN)
dp = Dispatcher(bot)

@dp.message_handler(commands=['start'])
async def start(message: types.Message):
    await message.reply("Salom! Menga video yoki musiqa havolasini yuboring.")

@dp.message_handler()
async def download_media(message: types.Message):
    url = message.text
    ydl_opts = {
        'format': 'bestaudio/best',
        'outtmpl': '%(title)s.%(ext)s',
    }
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        info = ydl.extract_info(url, download=True)
        filename = ydl.prepare_filename(info)
        await message.reply_document(open(filename, 'rb'))

if __name__ == '__main__':
    executor.start_polling(dp)

