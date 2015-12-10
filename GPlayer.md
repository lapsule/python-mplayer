# Synopsis #

```
GPlayer(args=(), stdout=PIPE, stderr=None, autospawn=True)
```

This is a `Player` subclass with GTK/GObject integration. It is meant to be used together with the GTK/GObject event loop to take advantage of the publish-subscribe system.

# Sample Usage #

```
import gtk
from mplayer.gtk2 import GPlayer

player = GPlayer(autospawn=False)
player.args = ['-really-quiet', '-msglevel', 'global=6']

def handle_data(data):
    if not data.startswith('EOF code'):
        print('log: %s' % (data, ))
    else:
        # Terminate MPlayer
        player.quit()
        # Quit the application
        gtk.main_quit()

# Connect a subscriber to MPlayer's stdout
player.stdout.connect(handle_data) # In 0.6.0: player.stdout.hook(handle_data)

# Manually spawn the MPlayer process
player.spawn()

# Play a file
player.loadfile('/path/to/file.mkv')

# Enter the GTK event loop
gtk.main()
```