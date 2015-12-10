# Synopsis #

```
QPlayerView(parent=None, args=(), stderr=None)
```

| **Name** | **Default Value** | **Valid Values** | **Description**|
|:---------|:------------------|:-----------------|:|
| `args`   | `()`              | tuples           | additional MPlayer arguments |
| `stderr` | `None`            | `None`, `subprocess.STDOUT`, or any writable `file` object | handle for MPlayer's stderr |

`QPlayerView` is a subclass of `QtGui.QX11EmbedContainer` (or `QtGui.QWidget` in MS Windows). Because of this, it can be readily used in Qt applications. Moreover, it uses `QtPlayer` internally to control MPlayer.

## Properties ##
| **Name** | **Description**|
|:---------|:|
| `player` | `QtPlayer` instance |

## Custom Signals ##
| **Name** | **Parameters** | **Emitted when** |
|:---------|:---------------|:-----------------|
| `eof`    | EOF code (type: int) | 'EOF code' is seen in MPlayer's stdout |

# Sample Usage #

```
import sys
from PyQt4 import QtGui
from mplayer.qt4 import QPlayerView

app = QtGui.QApplication(sys.argv)
# Main widget
w = QtGui.QWidget()
w.resize(640, 480)
w.setWindowTitle('QtPlayer')

# Create a player view
v = QPlayerView(w)

# Quit application after file has finished playing
v.eof.connect(app.closeAllWindows)

v.resize(640, 480)
w.show()

# Play the file
v.player.loadfile('/path/to/file.mkv')

# Enter the Qt event loop
sys.exit(app.exec_())
```