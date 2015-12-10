# Synopsis #

```
GtkPlayerView(args=(), stderr=None)
```

| **Name** | **Default Value** | **Valid Values** | **Description**|
|:---------|:------------------|:-----------------|:|
| `args`   | `()`              | tuples           | additional MPlayer arguments |
| `stderr` | `None`            | `None`, `subprocess.STDOUT`, or any writable `file` object | handle for MPlayer's stderr |

`GtkPlayerView` is a subclass of `gtk.Socket`. Because of this, it can be readily used in GTK applications. Moreover, it uses `GPlayer` internally to control MPlayer.

## Properties ##
| **Name** | **Description**|
|:---------|:|
| `player` | `GPlayer` instance |

## Custom Signals ##
| **Name** | **Parameters** | **Emitted when** |
|:---------|:---------------|:-----------------|
| `eof`    | EOF code (type: int) | 'EOF code' is seen in MPlayer's stdout |

# Sample Usage #

```
import gtk
from mplayer.gtk2 import GtkPlayerView

# Create a window
w = gtk.Window()
w.set_size_request(640, 480)
w.set_title('GtkPlayer')

# Quit application when this window is closed
w.connect('destroy', gtk.main_quit)

# Create a player view
v = GtkPlayerView()

# Quit application after file has finished playing
v.connect('eof', gtk.main_quit)

w.add(v)
w.show_all()

# Play the file
v.player.loadfile('/path/to/file.mkv')

# Enter the GTK event loop
gtk.main()
```