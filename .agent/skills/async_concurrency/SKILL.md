---
name: async-concurrency
description: Experto en programación asíncrona y concurrencia Python. asyncio, threading y multiprocessing.
---

# ⚡ Async & Concurrency Expert

Skill para implementar código asíncrono y concurrente en Python.

## Cuándo Usar

- Operaciones I/O intensivas (APIs, archivos)
- Procesamiento paralelo de datos
- Tareas en background
- Websockets y streaming

---

## 🔄 asyncio Básico

```python
import asyncio

async def fetch_data(url: str) -> dict:
    """Función asíncrona."""
    await asyncio.sleep(1)  # Simula I/O
    return {"url": url, "data": "..."}

async def main():
    # Ejecutar secuencialmente
    result1 = await fetch_data("url1")
    result2 = await fetch_data("url2")

    # Ejecutar en paralelo
    results = await asyncio.gather(
        fetch_data("url1"),
        fetch_data("url2"),
        fetch_data("url3"),
    )

asyncio.run(main())
```

---

## 🌐 HTTP Asíncrono con aiohttp

```python
import aiohttp
import asyncio

async def fetch_url(session: aiohttp.ClientSession, url: str) -> str:
    async with session.get(url) as response:
        return await response.text()

async def fetch_all(urls: list[str]) -> list[str]:
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_url(session, url) for url in urls]
        return await asyncio.gather(*tasks)

urls = ["https://api1.com", "https://api2.com"]
results = asyncio.run(fetch_all(urls))
```

---

## 🔀 Threading vs Multiprocessing

```python
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

# Threading: I/O bound (red, archivos)
def download_file(url: str) -> bytes:
    return requests.get(url).content

with ThreadPoolExecutor(max_workers=5) as executor:
    results = list(executor.map(download_file, urls))

# Multiprocessing: CPU bound (cálculos)
def process_data(data: list) -> float:
    return sum(x**2 for x in data)

with ProcessPoolExecutor(max_workers=4) as executor:
    results = list(executor.map(process_data, data_chunks))
```

---

## 🎯 Patrones Comunes

### Semáforo (limitar concurrencia)

```python
semaphore = asyncio.Semaphore(10)  # Max 10 concurrentes

async def limited_fetch(url: str):
    async with semaphore:
        return await fetch_data(url)
```

### Timeout

```python
try:
    result = await asyncio.wait_for(fetch_data(url), timeout=5.0)
except asyncio.TimeoutError:
    print("Timeout!")
```

### Background Tasks

```python
async def background_task():
    while True:
        await process_queue()
        await asyncio.sleep(60)

# Ejecutar en background
task = asyncio.create_task(background_task())
```

---

## ✅ Checklist

- [ ] ¿asyncio para I/O bound?
- [ ] ¿multiprocessing para CPU bound?
- [ ] ¿Semáforos para limitar concurrencia?
- [ ] ¿Timeouts configurados?
- [ ] ¿Manejo de excepciones en tasks?
