# python-async-cheat-sheet
Cheat sheet for high level asyncio functions in Python

## Instantiating classes with async initialization

Utilize factory pattern:
```python
class Client:

    async def _init(self):
        r, w = await asyncio.open_connection('127.0.0.1', 8888)
        self.reader = r
        self.writer = w
        print("Connected to server")
        await asyncio.gather(self.get_commands(), self.get_server_commands())
```
Instiate using factory method:
```python
async def create_client():
    c = Client()
    await c._init()
    return c
```

## Get user input async
```python
    async def ainput(self, string: str) -> str:
        await asyncio.get_event_loop().run_in_executor(
            None, lambda s=string: sys.stdout.write(s + ''))
        return await asyncio.get_event_loop().run_in_executor(
            None, sys.stdin.readline)
```

## Send data with a StreamWriter
```python
    async def send(self, data: str):
        if data and self.writer:
            self.writer.write(data.encode())
            await self.writer.drain()
```

## Read data with a StreamReader (I.e from server or other p2p chat client)
```python
    async def get_server_commands(self):
        while True:
            await asyncio.sleep(0) # needed in an inf loop to yield to other async branches
            line = await self.reader.readline()
            if not line: # empty byte string if server/other client disconnects
                break

            line = line.decode('latin1').rstrip()
            if line:
                print(f'>>> {line}')
```

## Perform async task for code that is not asyncio compatible:
Reference: https://docs.python.org/3/library/asyncio-eventloop.html#asyncio.loop.run_in_executor
