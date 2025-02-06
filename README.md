# media-search-bot
musiqa topish uchun eng yaxshi varyant
import logging
from aiogram import Bot, Dispatcher, types
from aiogram.types import InputFile
from aiogram.utils import executor
import yt_dlp
import os

# Bot token
TOKEN = "7667662544:AAEJ2f-6GDp-BIObURJIJkg6sddx5cnOb-w"

# Initialize bot and dispatcher
bot = Bot(token=TOKEN)
dp = Dispatcher(bot)

# Configure logging
logging.basicConfig(level=logging.INFO)

def download_audio(url):
    ydl_opts = {
        'format': 'bestaudio/best',
        'extract_audio': True,
        'audio_format': 'mp3',
        'outtmpl': 'downloads/%(title)s.%(ext)s'
    }
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        info = ydl.extract_info(url, download=True)
        filename = ydl.prepare_filename(info).replace(".webm", ".mp3").replace(".m4a", ".mp3")
        return filename

@dp.message_handler(commands=['start'])
async def start_command(message: types.Message):
    await message.reply("Salom! Men YouTube videolaridan musiqa yuklab beruvchi botman. Videoning havolasini yuboring!")

@dp.message_handler()
async def download_music(message: types.Message):
    url = message.text
    await message.reply("Musiqa yuklab olinmoqda... Bir oz kuting.")
    try:
        filename = download_audio(url)
        audio = InputFile(filename)
        await message.reply_audio(audio)
        os.remove(filename)
    except Exception as e:
        await message.reply(f"Xatolik yuz berdi: {e}")

if __name__ == "__main__":
    executor.start_polling(dp, skip_updates=True)
