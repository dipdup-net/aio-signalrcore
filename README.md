# SignalR core client (async fork)
[![Pypi](https://img.shields.io/pypi/v/aiosignalrcore.svg)](https://pypi.org/project/aiosignalrcore/)

This is asyncio version of the original SignalR Core [library](https://github.com/mandrewcito/signalrcore).  
The main difference is that `websocket` library is replaced with asyncio-compatabile `websockets`.  
All future changes in the original repo will be merged to this fork inheriting the major and minor version number.

**DEPRECATED: use [pysignalr](https://github.com/baking-bad/pysignalr) instead**

## Installation

```
pip install aiosignalrcore
```

## Usage

Check out the [docs](https://github.com/mandrewcito/signalrcore) of the original library.  
The names of all modules, classes, and methods are preserved.  

### Async example

```python
import asyncio
import logging
from aiosignalrcore.hub_connection_builder import HubConnectionBuilder
from aiosignalrcore.messages.completion_message import CompletionMessage
from aiosignalrcore.transport.websockets.connection import ConnectionState


hub_connection = (
    HubConnectionBuilder()
        .with_url('%URL%')
        .with_automatic_reconnect(
            {
                "type": "raw",
                "keep_alive_interval": 10,
                "reconnect_interval": 5,
                "max_attempts": 5,
            }
        )
        .build()
)


async def on_connect():
    logging.info('Connected to the server')
    
    while hub_connection.transport.state != ConnectionState.connected:
        await asyncio.sleep(0.1)

    await hub_connection.send("SendMessage", [])


async def on_message(message):
    pass


def on_error(self, message: CompletionMessage):
    raise Exception(message.error)


hub_connection.on_open(on_connect)
hub_connection.on_error(on_error)
hub_connection.on("Message", on_message)

await hub_connection.start()

try:
    while hub_connection.transport.state == ConnectionState.connected:
        await asyncio.sleep(1)
except KeyboardInterrupt:
    pass
finally:
    await hub_connection.stop()
```
