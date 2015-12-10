# Synopsis #

```
QtPlayer(args=(), stdout=PIPE, stderr=None, autospawn=True)
```

This is a `Player` subclass with Qt integration. It is meant to be used together with the Qt event loop to take advantage of the publish-subscribe system.

# Sample Usage #

```
import sys
from PyQt4 import QtCore
from mplayer.qt4 import QtPlayer

app = QtCore.QCoreApplication(sys.argv)
# Here, autospawn is True so no need to manually call player.spawn() later
player = QtPlayer(['-really-quiet', '-msglevel', 'global=6'])

def handle_data(data):
    if not data.startswith('EOF code'):
        print('log: %s' % (data, ))
    else:
        # Terminate MPlayer
        player.quit()
        # Quit the application
        app.quit()

# Connect a subscriber to MPlayer's stdout
player.stdout.connect(handle_data) # In 0.6.0: player.stdout.hook(handle_data)

# Play a file
player.loadfile('/path/to/file.mkv')

# Enter the Qt event loop
sys.exit(app.exec_())
```