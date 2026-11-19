# server.py

import asyncio
from contextlib import asynccontextmanager
from fastapi import FastAPI
from database import init_db
from bot import bot, dp

@asynccontextmanager
async def lifespan(app: FastAPI):
    await init_db()
    task = asyncio.create_task(dp.start_polling(bot))
    yield
    task.cancel()
    try:
        await task
    except asyncio.CancelledError:
        pass
    await bot.session.close()

app = FastAPI(lifespan=lifespan)

@app.get("/")
async def home():
    return {"status": "ok", "shop": "Rozmado"}
    
