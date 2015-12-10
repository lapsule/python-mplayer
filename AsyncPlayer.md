# Synopsis #

```
AsyncPlayer(args=(), stdout=PIPE, stderr=None, autospawn=True, map=None)
```

This is a `Player` subclass with asyncore integration. It is meant to be used together with `asyncore.loop` to take advantage of the publish-subscribe system.

The optional parameter `map` corresponds to the mapping (dictionary) that will be used with `asyncore.loop()`. By default, this is the module global `asyncore.socket_map`. A different mapping can be specified.

# Sample Usage #

```
import asyncore
from mplayer.async import AsyncPlayer

# Don't autospawn because we want to setup the args later
player = AsyncPlayer(autospawn=False)

# Setup additional args
player.args = ['-really-quiet', '-msglevel', 'global=6']

def handle_data(data):
    if not data.startswith('EOF code'):
        print('log: %s' % (data, ))
    else:
        player.quit()

# hook a subscriber to MPlayer's stdout
player.stdout.hook(handle_data)

# Manually spawn the MPlayer process
player.spawn()

# play a file
player.loadfile('/path/to/file.mkv')

# run the asyncore event loop
asyncore.loop()
```